pipeline {
    agent any

    parameters {
        choice(name: 'ENV_PARAM', choices: ['dev', 'qat', 'prod'], description: 'Select the environment to deploy.')
        choice(name: 'BRANCH_PARAM', choices: ['main', 'feature/branch'], description: 'Select the branch to pull code from.')
        booleanParam(name: 'APPLY', defaultValue: false, description: 'Check to apply the Terraform changes.')
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Create or switch to the workspace
                    dir("terraform_workspace/${params.BRANCH_PARAM}") {
                        git branch: "${BRANCH_PARAM}", url: 'https://github.com/kjaindevops/terraform.git'
                    }
                }
            }
        }

        stage('Terraform Plan') {
            when {
                expression { !params.APPLY && params.ENV_PARAM ==~ /(dev|qat|prod)/ }
            }
            steps {
                script {
                    // Run Terraform plan
                    sh "terraform init"
                    sh "terraform workspace select ${params.ENV_PARAM}"
                    sh "terraform plan"
                }
            }
        }

        stage('Terraform Apply') {
            when {
                expression { params.APPLY && params.ENV_PARAM ==~ /(dev|qat|prod)/ }
            }
            steps {
                script {
                    // Run Terraform apply
                    sh "terraform init"
                    sh "terraform workspace select ${params.ENV_PARAM}"
                    sh "terraform apply -auto-approve"
                }
            }
        }
    }
}
