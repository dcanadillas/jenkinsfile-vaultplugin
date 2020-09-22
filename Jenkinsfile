pipeline {
  agent any
  parameters {
    string(name: 'vaulturl', defaultValue: 'http://localhost:8200', description: 'Vault API URL')
    string(name: 'vaultpath', defaultValue: 'kv/cicd', description: 'Secrets path for Vault secrets')
  }
  stages {
    stage ('One') {
      steps {
        withVault(
          configuration: [
            failIfNotFound: true, 
            timeout: 10, 
            vaultCredentialId: 'vault-app-role', 
            vaultUrl: "${params.vaulturl}"
          ], 
          vaultSecrets: [
            [
              path: "${params.vaultpath}", 
              secretValues: [
                [envVar: 'tfetoken', vaultKey: 'tfe_token'],
                [envVar: 'tfeorg', vaultKey: 'tfe_token']
              ]
            ]
          ]
        ) {
            echo "${env.tfe_token}"
            sh """
            curl -H "Authorization: Bearer ${env.tfetoken}" \
            -H "Content-Type: application/vnd.api+json" \
            -X GET https://app.terraform.io/api/v2/organizations/${env.tfeorg}/workspaces
            echo 'Hello'
            """
          }
      }
    }
    stage ('Two') {
      steps {
        withVault(
          configuration: [
            failIfNotFound: true, 
            timeout: 10, 
            vaultCredentialId: 'vault-app-role', 
            vaultUrl: "${params.vaulturl}"
          ],
          vaultSecrets: [
            [
              path: "${params.vaultpath}", 
              secretValues: [
                [envVar: 'ghuser', vaultKey: 'gh_user'],
                [envVar: 'ghtoken', vaultKey: 'gh_token']
              ]
            ]
          ]
        ) {
            sh """
            curl -H "Authorization: ${ghtoken}" "https://api.github.com/users/${ghuser}/repos" | grep -o "git@[^\"]*"
            """
          }   
      }
    }
  }
}