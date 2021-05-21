# Introduction to Vault

## Project Tasks

#### [ \ ] Install Vault

 * Add HashiCorp GPG key
 * Add official HashiCorp Linux repository
 * Update and Install
 * Verify the installation
   - run `vault`

#### [ \ ] Start a Vault *dev server*

  * **DEV SERVER**: a built-in, pre-configured server. Not very secure but useful for local use of Vault!
  * To start the dev server, run`vault server -dev`
  * Configure Vault client to talk to dev server
    - `export VAULT_ADDR='http://127.0.0.1:8200'`taken from output when the dev server was started. Vault CLI determines which Vault servers to send requests using `VAULT_ADDR`
    - Provide Vault with valid token `export VAULT_TOKEN="xxxxxx"` using the generated *Root Token** from output when dev server was started.
  * Verify that the server is running
    - Run `vault status`

#### [ \ ] Write Your First Secret

  *  Use command `vault kv put <path> <key>=<value>`
     - Writes a secret to Key/Value v2 secrets engine
  * example `vault kv put secret/hello foo=world`
    - In example, the path is `secret/`. Other paths are available.
    - Multiple pieces of data can be written all at once `vault kv put secret/hello foo=world excited=yes`
    - Pre-existing data at the path can be replaced with a new version. The output of the `vault kv put` commands defines the version.

#### [ \  ] Getting a Secret  

  * Use command `vault kv get <path>` to retrieve secrets
    - example `vault kv get secret/hello`
  * Use flag `-field=<key_name>`to only print the value of a given field
    - example `vault kv get -field=excited secret/hello` which would print `yes`
  * To get an optional JSON output, use the `jq` tool
    - example `vault kv get -field=excited secret/hello | jq -r .data.data.excited`

#### [ \ ] Deleting a Secret

   * Use command `vault kv delete`
      - example `vault kv delete secret/hello`

#### [ \ ] Enable AWS Secrets Engine ([Dynamic Secrets](#dynamic-secrets))

    *  To enable use command `vault secrets enable aws`
      - By default, the path will be `aws/`
      - Use `-path` to define a different paths
    * Configure creds that Vault will sue to communicate with AWS to generate IAM credentials
    ```
    $vault write aws/config/root \
    access_key=<key-here> \
    secret_key=<key-here> \
    region=us-east-1
    ```

#### [ \ ] Configure AWS Secrets Engine

 * Configure a Vault role that maps to a set of permissions in AWS as well as AWS credential type. When users generate credentials, they are generated against the specified role.

ex:

```
$ vault write aws/roles/my-role \
    credential_type=iam_user \
    policy_document=-<<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:*",
      "Resource": "*"
    }
  ]
}
EOF
```

This creates a role named **"my-role"**. When users generate credentials against this role, Vault will create an IAM user and attach the specified policy document to the IAM user. Vault will then create an access key and secret key for the IAM user and return these credentials.

  * To generate a new aws credential
      - Use command `$ vault read aws/creds/my-role` which will read from the `/creds` endpoint with the name of the role.
      - Each time the command is run, a new set of credentials will generate.

#### [ ] Deploy Vault
>> Press Ctrl+C to terminate the dev server that is running at http://127.0.0.1:8200 before proceeding <<<

#### [ ] Use the HTTP APIs with Authentication

## Questions, comments, concerns, etc.

### What is Vault?

Vault is a tool for securely managing access to secrets. It is a centralized storage for all secrets. Vault uses an API server which performs operations and client requests on secrets. Vault uses multiple types of [secret engines](#what-are-secrets-engines) which can support each type of secret that can be supported.

Secrets are sets of different credentials such as *authentication* to a system or *authorization* to a system such as database usernames and passwords, API tokens, or TLS certificates.

### What problems does Vault solve?

Vault addresses the problem of managing sensitive information. Database credentials and API Keys are some examples of sensitive information that we need to store and make available to our applications in a secure way. Vault controls all aspects of a sensitive piece of information: its generation, storage, usage and its revocation. Applications will not have to store them at the application level. Instead, applications can call for the secrets to Vault.  Vault encrypts all data with an encryption key before storing the secret. This key is then encrypted by another key. By using this model, only encrypted versions of they key are shared between applications and Vault, ensuring encryption at rest at the application level.

Vaults key feature to help solve the task of securely managing access to secrets are:

#### **DYNAMIC SECRETS**
Dynamic secrets are generated when they are accessed. This means that the dynamic secret does not exist until they are read. This reduces the risk of theft or another client using the same secrets.  Built-in vocation allows dynamic secrets to be revoke3d immediately after use, minimizing the lifespan of the secret.

#### **DATA ENCRYPTION**
Vault provides "encryption as a service" by encrypting data in transit (with TLS) and at rest (using AES 256-bitCBC encryption). Vault can encrypt and decrypt data without storing it, which allows security teams to define encryption parameters and developers to store the encrypted data without having to design their own encryption methods.

#### **REVOCATION**
Vault has built-in support for secret revocation at scale. Zthis means that Vault can revoke single secrets or a whole tree of secrets; for example a user that has multiple secrets of a particular type. Key rolling and system lock downs can be triggered in the case of intrusion. A real world example is 2FA where the keys get rotated after a certain amount of seconds.

#### **SECURE SECRET STORAGE**
KV secrets can be stored in Vault and can encrypt the secrets prior to writing them to storage. Gaining access to the raw storage isn't enough to access the secrets. Vault can write to disk, CONSUL, and more.

#### **LEASING AND RENEWAL**
Secrets in Vault can be given a lease associated with them. The lease can be set to automatically be revokes after any specified time. Secret renewals can be built-in via renew APIs.

### What are Secrets Engines?

Vault behaves like a virtual file system. READ, WRITE, DELETE list operations are forwarded to corresponding secrets engines. Secrets engines then decide what to do with those operations.

"...enables Vault to interface directly with physical systems, databases, HSMs, etc. But in addition to these physical systems, Vault can interact with more unique environments like AWS IAM, dynamic SQL user creation, etc. all while using the same read/write interface."
