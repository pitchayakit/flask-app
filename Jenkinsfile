pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'python-flask-app'
        DOCKER_TAG = "${BUILD_NUMBER}"
        VENV = ".venv"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python Virtual Environment') {
            steps {
                script {
                    // Create and activate virtual environment
                    sh '''
                        python3 -m venv ${VENV}
                        . ${VENV}/bin/activate
                        python3 -m pip install --upgrade pip
                    '''
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install dependencies in virtual environment
                    sh '''
                        . ${VENV}/bin/activate
                        pip install -r requirements.txt
                    '''
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Run tests in virtual environment
                    sh '''
                        . ${VENV}/bin/activate
                        python -m pytest tests/
                    '''
                }
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
        always {
            // Clean up virtual environment
            sh 'rm -rf ${VENV}'
        }
        failure {
            echo 'Pipeline failed! Sending notification...'
        }
        success {
            echo 'Pipeline succeeded! Application deployed successfully.'
        }
    }
} 