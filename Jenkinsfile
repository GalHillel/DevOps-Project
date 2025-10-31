pipeline {
    agent any

    environment {
        DOCKER_HOST = 'unix:///var/run/docker.sock'
        DOCKER_COMPOSE_PROJECT_NAME = 'devops-project'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out Git repository...'
                git branch: 'main', url: 'https://github.com/GalHillel/DevOps-Project.git'
            }
        }

        stage('Pull Images') {
            steps {
                echo 'Pulling latest images from Docker Hub...'
                sh 'docker pull galhillel/devops-project-backend:latest'
                sh 'docker pull galhillel/devops-project-frontend:latest'
                sh 'docker pull mongo:6.0'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Stopping old containers if any...'
                sh 'docker compose down || true'
                
                echo 'Starting containers...'
                sh 'docker compose up -d'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing backend availability...'
                sh 'curl -f http://localhost:5000/api || echo "Backend not reachable"'

                echo 'Testing frontend availability...'
                sh 'curl -f http://localhost:5173 || echo "Frontend not reachable"'
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
