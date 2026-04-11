@Library('sharedLib') _
pipeline {
    agent any

    environment {
        
    COMPOSE_FILE = "docker-compose.yml"
        IMAGE_TAG = "${BRANCH_NAME}-${BUILD_NUMBER}"
       
    }

    stages {

        stage('Checkout Source') {
            steps {
                checkoutSource()
            }
        }

        stage('Docker Login') {
            steps {
                dockerLogin('dockerhub-creds')
            }
        }

        stage('Build Docker Images') {
            steps {
                buildImages('./backend', './frontend', IMAGE_TAG)
                
            }
        }

        stage('Push Docker Images') {
            steps {
                pushImages()
            }
        }

        stage('Prepare .env for Compose') {
            steps {
                prepareEnvFile()
            }
        }

        stage('Approval for Staging/Prod Deploy') {
            when {
                anyOf {
                    branch 'stg'
                    branch 'prd'
                }
            }
            steps {
                input message: "Deploy to ${BRANCH_NAME} environment?", ok: "Yes, Deploy"
            }
        }

        stage('Deploy') {
            steps {
                deployApp(COMPOSE_FILE, env.BRANCH_NAME)
 
            }
        }

        stage('Cleanup Local Images') {
            steps {
                cleanupImages()

            }
        }

    }

    post {
        success {
            echo "Pipeline succeeded!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
