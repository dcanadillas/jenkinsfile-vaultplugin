# Jenkins and Vault pipeline example

This is a pipeline example that using your secrets in a Vault instance it is able to:
* Connects to your GitHub account by `curl`request to list your repos
* Connects to Terraform Cloud to list your current workspaces in an Organization

The goal is about showing the Jenkins Vault Plugin in action with an easy pipeline execution

## Prepare your Environment

You need to have an account in Terraform Cloud (TFC) and a Vault server running. I you don't, you can do the following:
* [Sign up into TFC/TFE](https://app.terraform.io/signup) and follow the process to create your first organization
* For a quick shot just run Vault in development mode with (it will run on your localhost on port 9200):
  ```bash
  vault server -dev -dev-listen-address="127.0.0.1:9200" -dev-root-token-id="root"
  ```
> NOTE: When you are running Vault in development mode everything is in memory, so data is not persisted. In that case you need to run Vault in [server mode](https://learn.hashicorp.com/tutorials/vault/getting-started-deploy)


Once you have TFC account and Vault running:

* Create some tokens in your TFC Org:
  - Create an [organization token]({"data":{"gh_user"="dcanadillas"}}
  - Create a [team token](https://www.terraform.io/docs/cloud/users-teams-organizations/api-tokens.html#team-api-tokens)
* Change the values in the included file `secrets.json` with your values (GitHub token, TFC tokens and TFC org name)
* Create your secrets in Vault (here are the commands, but you can do it in the UI):
  ```bash
  export VAULT_ADDR="127.0.0.1:9200"
  ```
  ```bash
  vault login root
  ```
  ```bash
  vault secrets enable -path=kv -version=2 kv
  ```
  ```bash
  vault write kv/data/cicd @secrets.json
  ```

Configure your Jenkins with Vault Plugin installed. You can take a look at [this repo]() using JCasC to configure from scratch. I adds the Jenkinsfile pipeline from this repo.
