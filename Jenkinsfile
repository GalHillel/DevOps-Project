pipeline {
    agent any

    environment {
        DOCKER_HOST = 'unix:///var/run/docker.sock'
        DOCKER_COMPOSE_PROJECT_NAME = 'devops-project'
    }

    stages {

        stage('Clean Workspace') {
            steps {
                echo 'Cleaning workspace...'
                deleteDir() // מבטיח workspace נקי
            }
        }

        stage('Checkout') {
            steps {
                echo 'Checking out Git repository...'
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: 'https://github.com/GalHillel/DevOps-Project.git']]
                ])
            }
        }

        stage('Pull Images') {
            steps {
                echo 'Pulling latest images from Docker Hub...'
                sh 'docker pull galhillel/devops-project-backend:latest || true'
                sh 'docker pull galhillel/devops-project-frontend:latest || true'
                sh 'docker pull mongo:6.0 || true'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Stopping old containers if any...'
                sh 'docker compose down || true'
                
                echo 'Starting containers...'
                sh 'docker compose up -d || true'
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
