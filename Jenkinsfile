pipeline {
    agent any

    environment {
        GHCR_USERNAME = "ahad9049"
        FRONTEND_IMAGE = "ghcr.io/${GHCR_USERNAME}/three-tier-app-frontend"
        BACKEND_IMAGE  = "ghcr.io/${GHCR_USERNAME}/three-tier-app-backend"
        GHCR_CREDENTIALS_ID = "ghcr"
        BUILD_TAG = "${env.BUILD_NUMBER}"
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
                    env.FRONTEND_TAG = "${FRONTEND_IMAGE}:${BUILD_TAG}"
                    env.BACKEND_TAG  = "${BACKEND_IMAGE}:${BUILD_TAG}"

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

        stage('Cleanup Local Images') {
            steps {
                sh """
                docker rmi -f $FRONTEND_TAG || true
                docker rmi -f $BACKEND_TAG || true
                """
            }
        }

        stage('Update docker-compose.yml') {
            steps {
                sh """
                yq -i ".services.frontend.image = \\"${FRONTEND_IMAGE}:${BUILD_TAG}\\"" docker-compose.yml
                yq -i ".services.backend.image = \\"${BACKEND_IMAGE}:${BUILD_TAG}\\"" docker-compose.yml
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
