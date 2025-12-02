pipeline {
    agent any
    
    environment {
        AWS_REGION = "us-east-1"
    }
    
    options {
        timestamps()
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: "main",
                    url: 'https://github.com/joelwalice/Terraform-Pipeline-structure.git'
            }
        }

        stage('Init') {
            steps {
                script {
                    withAWS(credentials: 'Access Keys', region: "${AWS_REGION}") {
                        sh """
                            terraform --version
                            terraform init -input=false
                        """
                    }
                }
            }
        }

        stage('Plan') {
            steps {
                script {
                    withAWS(credentials: 'aws-creds', region: "${AWS_REGION}") {
                        sh """
                            terraform plan -out=tfplan
                        """
                    }
                }
            }
        }

        stage('Approval') {
            when {
                branch 'main'
            }
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    input message: "Approve Terraform Apply?"
                }
            }
        }

        stage('Apply') {
            when {
                branch 'main'
            }
            steps {
                script {
                    withAWS(credentials: 'aws-creds', region: "${AWS_REGION}") {
                        sh """
                            terraform apply -auto-approve tfplan
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finished"
        }
    }
}
