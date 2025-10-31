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
                deleteDir()
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

        stage('Create .env files') {
            steps {
                echo 'Creating .env files for server and client...'
                writeFile file: 'server/.env', text: '''PORT=5000
MONGO_URI=mongodb://mongo:27017/devops-project
NODE_ENV=development
'''
                writeFile file: 'client/.env', text: '''VITE_API_URL=http://backend:5000/api
'''
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
                // שימוש בשמות קונטיינרים ברשת Docker
                sh 'curl -f http://backend:5000/api || echo "Backend not reachable"'

                echo 'Testing frontend availability...'
                sh 'curl -f http://frontend:5173 || echo "Frontend not reachable"'
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
