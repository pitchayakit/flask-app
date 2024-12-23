pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'python-flask-app'
        DOCKER_TAG = "${BUILD_NUMBER}"
        VENV = ".venv"
        DOCKER_CREDENTIALS = 'docker-hub-credentials'
        DOCKER_REGISTRY = 'your-docker-registry'
        PYTHON_VERSION = "python3.12"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python Environment') {
            steps {
                script {
                    sh '''
                        # Install Python 3.12 venv package if not present
                        if ! dpkg -l | grep -q python3.12-venv; then
                            echo "Python3.12-venv not found. Installing..."
                            sudo apt update
                            sudo apt install -y python3.12-venv
                        fi
                        
                        # Create virtual environment with Python 3.12
                        ${PYTHON_VERSION} -m venv ${VENV}
                        
                        # Activate and upgrade pip
                        . ${VENV}/bin/activate
                        python3 -m pip install --upgrade pip
                    '''
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
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
                    sh '''
                        . ${VENV}/bin/activate
                        python -m pytest tests/
                    '''
                }
            }
        }

        stage('Docker Build and Push') {
            steps {
                script {
                    def dockerImage = docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh '''
                        docker ps -q --filter "name=flask-app" | grep -q . && \
                        docker stop flask-app && \
                        docker rm flask-app || echo "No container running"
                    '''
                    
                    sh """
                        docker run -d \
                            --name flask-app \
                            -p 5000:5000 \
                            ${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    sh 'sleep 10'
                    
                    sh '''
                        curl -f http://localhost:5000/health || exit 1
                        echo "Application health check passed"
                    '''
                }
            }
        }
    }

    post {
        always {
            sh '''
                rm -rf ${VENV}
                docker image prune -f
            '''
        }
        success {
            echo 'Pipeline succeeded! Application deployed successfully.'
        }
        failure {
            echo 'Pipeline failed! Sending notification...'
            sh '''
                docker ps -q --filter "name=flask-app" | grep -q . && \
                docker stop flask-app && \
                docker rm flask-app || echo "No container running"
            '''
        }
    }
} 