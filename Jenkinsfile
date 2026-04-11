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
                buildImages('./backend', './frontend', IMAGE_TAG')
                
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
