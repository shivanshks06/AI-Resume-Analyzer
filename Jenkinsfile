pipeline {
    agent any

    environment {
        APP_NAME = "ai-resume-analyzer"
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Build Docker Images') {
            steps {
                echo 'Building Docker container...'
                sh 'docker compose build'
            }
        }

        stage('Test & Verify') {
            steps {
                echo 'Verifying Docker Compose configuration...'
                sh 'docker compose config'
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
        success {
            echo 'Deployment Successful! App is running on port 8501.'
        }
        failure {
            echo 'Pipeline failed. Check Jenkins logs for details.'
        }
    }
}
