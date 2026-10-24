pipeline {
    agent any

    environment {
        // Change this to your Docker Hub username if different
        DOCKER_HUB_USER = "shivanshks06"
        IMAGE_NAME      = "ai-resume-analyzer"
        IMAGE_TAG       = "${env.BUILD_NUMBER}"
        
        // Jenkins Credential ID for Docker Hub (Kind: Username with password)
        DOCKER_HUB_CREDENTIALS = "dockerhub-credentials"
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo 'Logging in and pushing image to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: "${DOCKER_HUB_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Deploy Application') {
            steps {
                echo 'Deploying application with Docker Compose...'
                sh 'docker compose down'
                sh 'docker compose up -d'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up dangling images...'
            sh 'docker image prune -f || true'
        }
        success {
            echo 'Pipeline succeeded! Image pushed to Docker Hub and application deployed.'
        }
        failure {
            echo 'Pipeline failed. Check Jenkins logs for details.'
        }
    }
}
