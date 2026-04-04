pipeline {
    agent any

    environment {
        GHCR_USERNAME = "ahad9049"
        FRONTEND_IMAGE = "ghcr.io/${GHCR_USERNAME}/three-tier-app-frontend"
        BACKEND_IMAGE  = "ghcr.io/${GHCR_USERNAME}/three-tier-app-backend"
        GHCR_CREDENTIALS_ID = "ghcr"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Ahad9049/three-tier-application.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    def tag = "${env.BUILD_NUMBER}"

                    env.FRONTEND_TAG = "${FRONTEND_IMAGE}:${tag}"
                    env.BACKEND_TAG  = "${BACKEND_IMAGE}:${tag}"

                    sh """
                    docker build -t $FRONTEND_TAG ./frontend
                    docker build -t $BACKEND_TAG ./backend
                    """
                }
            }
        }

        stage('Push to GHCR') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: GHCR_CREDENTIALS_ID,
                    usernameVariable: 'GHCR_USER',
                    passwordVariable: 'GHCR_TOKEN'
                )]) {
                    sh """
                    echo \$GHCR_TOKEN | docker login ghcr.io -u \$GHCR_USER --password-stdin
                    docker push $FRONTEND_TAG
                    docker push $BACKEND_TAG
                    """
                }
            }
        }

        stage('Cleanup') {
            steps {
                sh """
                docker rmi -f $FRONTEND_TAG || true
                docker rmi -f $BACKEND_TAG || true
                """
            }
        }

        stage('Update docker-compose') {
            steps {
                sh """
                yq -i ".services.frontend.image = \"three-tier-app-frontend:${BUILD_NUMBER}\"" docker-compose.yml
                yq -i ".services.backend.image = \"three-tier-app-backend:${BUILD_NUMBER}\"" docker-compose.yml
                """
            }
        }

        stage('Run Docker Compose') {
            steps {
                sh """
                docker-compose down
                docker-compose pull
                docker-compose up -d
                """
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline succeeded!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
