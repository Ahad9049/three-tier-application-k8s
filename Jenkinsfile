pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_URI = "116429398424.dkr.ecr.us-east-1.amazonaws.com"
        AWS_ECR_REPO_FRONTEND = "three-tier-app-frontend"
        AWS_ECR_REPO_BACKEND = "three-tier-app-backend"
        GITHUB_CREDENTIALS = "github"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Ahad9049/three-tier-application.git'
            }
        }

        stage('Login to AWS ECR') {
    steps {
        withCredentials([[
            
            credentialsId: 'aws-creds',   
            accessKeyVariable: 'AWS_ACCESS_KEY_ID',
            secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
        ]]) {
            sh '''
            aws cnfigure set aws_access_key_id $AWS_ACCESS_KEY_ID  
            aws cnfigure set aws_secret_access_key_id $AWS_SECRET_ACCESS_KEY_ID
            aws cnfigure set default.region ${AWS_REGION}

            aws ecr get-login-password --region $AWS_REGION | \
            docker login --username AWS --password-stdin ${ECR_URI}
            '''
        }
    }
}

        stage('Build Docker Images') {
            steps {
                script {
                    env.FRONTEND_TAG = "${ECR_URI}/${AWS_ECR_REPO_FRONTEND}:${BUILD_TAG}"
                    env.BACKEND_TAG  = "${ECR_URI}/${AWS_ECR_REPO_BACKEND}:${BUILD_TAG}"

                    sh """
                    docker build -t ${env.FRONTEND_TAG} ./frontend
                    docker build -t ${env.BACKEND_TAG} ./backend
                    """
                }
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

        stage('Update docker-compose.yml') {
            steps {
                sh """
                sed -i 's|${ECR_URI}/.*three-tier-app-backend:.*|${env.BACKEND_TAG}|' docker-compose.yml
                sed -i 's|${ECR_URI}/.*three-tier-app-frontend:.*|${env.FRONTEND_TAG}|' docker-compose.yml
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
            echo "Pipeline succeeded!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
