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

[ ] Deploy Vault

[ ] Use the HTTP APIs with Authentication

## Questions, comments, concerns, etc.

### What is Vault?

Vault is a tool for securely managing access to secrets.

Secrets are sets of different credentials such as *authentication* to a system or *authorization* to a system such as database usernames and passwords, API tokens, or TLS certificates.

### What problems does Vault solve?

Vault provides a central location which promises to encrypt everything both at rest inside of Vault as well as in transit between Vault and any of the clients that want to access it.

Vaults key feature to help solve the task of securely managing access to secrets are:

**DYNAMIC SECRETS**

**DATA ENCRYPTION**

**REVOCATION**

**SECURE SECRET STORAGE**

**LEASING AND RENEWAL**

**REVOCATION**

### Why use Vault

Vault allows a granular approach to credentials and

### What are Secrets Engines?

Vault behaves like a virtual file system. READ, WRITE, DELETE list operations are forwarded to corresponding secrets engines. Secrets engines then decide how to react to those operations.
