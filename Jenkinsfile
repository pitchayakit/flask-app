pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'python-app'
        DOCKER_TAG = "${BUILD_NUMBER}"
        DOCKER_FULL_IMAGE = "${DOCKER_IMAGE}:${DOCKER_TAG}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_FULL_IMAGE}")
                }
            }
        }
        
        stage('Run Tests') {
            steps {
                script {
                    docker.image("${DOCKER_FULL_IMAGE}").inside {
                        sh 'python -m pytest tests/ || true'
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    // Stop existing container if it exists
                    sh '''
                        docker ps -f name=python-app -q | xargs --no-run-if-empty docker stop
                        docker ps -a -f name=python-app -q | xargs --no-run-if-empty docker rm
                    '''
                    
                    // Run new container
                    sh "docker run -d --name python-app-${BUILD_NUMBER} -p 5000:5000 ${DOCKER_FULL_IMAGE}"
                }
            }
        }
    }
    
    post {
        failure {
            // Clean up in case of failure
            sh '''
                docker ps -f name=python-app -q | xargs --no-run-if-empty docker stop
                docker ps -a -f name=python-app -q | xargs --no-run-if-empty docker rm
            '''
        }
    }
}
