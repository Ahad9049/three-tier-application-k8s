pipeline {
    label {agent 'ec2-dev'}

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub_creds'
        BRANCH_NAME = "${env.BRANCH_NAME}"
        IMAGE_TAG = "${BRANCH_NAME}-${BUILD_NUMBER}"
        DOCKERHUB_USER = "abdulahad9049"
    }

    stages {

        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }
        stage('Docker Login') {
            steps {
              
                withCredentials([usernamePassword(credentialsId: 'dockerhub_creds', 
                                                  usernameVariable: 'DOCKERHUB_USER', 
                                                  passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh '''
                        echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    env.FRONTEND_TAG = "${DOCKERHUB_USER}/three-tier-app-frontend:${IMAGE_TAG}"
                    env.BACKEND_TAG  = "${DOCKERHUB_USER}/three-tier-app-backend:${IMAGE_TAG}"

                    sh """
                    docker build -t ${env.FRONTEND_TAG} ./frontend
                    docker build -t ${env.BACKEND_TAG} ./backend
                    """
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                sh """
                docker push ${env.FRONTEND_TAG}
                docker push ${env.BACKEND_TAG}
                """
            }
        }

        stage('Prepare .env for Compose') {
            steps {
                script {
                    writeFile file: '.env', text: """
BACKEND_IMAGE=${env.BACKEND_TAG}
FRONTEND_IMAGE=${env.FRONTEND_TAG}
"""
                }
            }
        }

            steps {
                input message: "Deploy to ${BRANCH_NAME} environment?", ok: "Yes, Deploy"
            }
        }

        stage('Run Docker Compose') {
            steps {
                sh """
                docker-compose --env-file .env down
                docker-compose --env-file .env pull
                docker-compose --env-file .env up -d --remove-orphans
                """
            }
        }

        stage('Cleanup Local Images') {
            steps {
                sh """
                docker rmi -f ${env.FRONTEND_TAG} || true
                docker rmi -f ${env.BACKEND_TAG} || true
                """
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
