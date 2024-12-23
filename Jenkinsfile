pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'python-flask-app'
        DOCKER_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'python -m pytest tests/'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Stop existing container if running
                    sh 'docker ps -q --filter "name=flask-app" | grep -q . && docker stop flask-app && docker rm flask-app || echo "No container running"'
                    
                    // Run new container
                    sh "docker run -d --name flask-app -p 5000:5000 ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed! Sending notification...'
        }
        success {
            echo 'Pipeline succeeded! Application deployed successfully.'
        }
    }
} 