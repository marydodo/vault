# Introduction to Vault

## Project Tasks

[ \ ] Install Vault

 * Add HashiCorp GPG key
 * Add official HashiCorp Linux repository
 * Update and Install
 * Verify the installation
   - run `vault`

[ \ ] Start a Vault *dev server*

  * **DEV SERVER**: a built-in, pre-configured server. Not very secure but useful for local use of Vault!
  * To start the dev server, run`vault server -dev`
  * Configure Vault client to talk to dev server
    - `export VAULT_ADDR='http://127.0.0.1:8200'`taken from output when the dev server was started. Vault CLI determines which Vault servers to send requests using `VAULT_ADDR`
    - Provide Vault with valid token `export VAULT_TOKEN="xxxxxx"` using the generated *Root Token** from output when dev server was started.
  * Verify that the server is running
    - Run `vault status`

 [ \ ] Write Your First Secret

  *  Use command `vault kv put <path> <key>=<value>`
     - Writes a secret to Key/Value v2 secrets engine
  * example `vault kv put secret/hello foo=world`
    - In example, the path is `secret/`. Other paths are available.
    - Multiple pieces of data can be written all at once `vault kv put secret/hello foo=world excited=yes`
    - Pre-existing data at the path can be replaced with a new version. The output of the `vault kv put` commands defines the version.

[ \  ] Getting a Secret  

  * Use command `vault kv get <path>` to retrieve secrets
    - example `vault kv get secret/hello`
  * Use flag `-field=<key_name>`to only print the value of a given field
    - example `vault kv get -field=excited secret/hello` which would print `yes`
  * To get an optional JSON output, use the `jq` tool
    - example `vault kv get -field=excited secret/hello | jq -r .data.data.excited`

[ \ ] Deleting a Secret

   * Use command `vault kv delete`
      - example `vault kv delete secret/hello`

[ ] Enable/Configure AWS Secrets Engine

[ ] Deploy Vault

[ ] Use the HTTP APIs with Authentication

## Questions, comments, concerns, etc.

### What is Vault?

Vault is a tool for securely managing access to secrets. It is a storage for all secrets. Vault uses an API server which performs operations and client requests on secrets. Vault uses multiple types of [secret engines](#what-are-secrets-engines) which can support each type of secret that can be supported.

Secrets are sets of different credentials such as *authentication* to a system or *authorization* to a system such as database usernames and passwords, API tokens, or TLS certificates.


### What problems does Vault solve?

Vault addresses the problem of managing sensitive information.

Database credentials and API Keys are some examples of sensitive information that we need to store and make available to our applications in a secure way.

Vault controls all aspects of a sensitive piece of information: its generation, storage, usage and, last but not least, its revocation. Vault encrypts all data with an encryption key before writing it to the store. This encryption key is encrypted by yet another key – the master key.

Vaults key feature to help solve the task of securely managing access to secrets are:

**DYNAMIC SECRETS**

**DATA ENCRYPTION**

**REVOCATION**

**SECURE SECRET STORAGE**

**LEASING AND RENEWAL**

**REVOCATION**.

### What are Secrets Engines?

Vault behaves like a virtual file system. READ, WRITE, DELETE list operations are forwarded to corresponding secrets engines. Secrets engines then decide what to do with those operations.

"...enables Vault to interface directly with physical systems, databases, HSMs, etc. But in addition to these physical systems, Vault can interact with more unique environments like AWS IAM, dynamic SQL user creation, etc. all while using the same read/write interface."
