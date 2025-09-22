pipeline {
    agent any
    environment {
        APP_PORT = ''
        IMAGE_TAG = ''
    }
    stages {
        stage('Checkout') {
            steps {
               
                checkout scm
                script {
                   
                    if (env.BRANCH_NAME == 'main') {
                        APP_PORT = '3000'
                        IMAGE_TAG = 'nodemain:v1.0'
                    } else if (env.BRANCH_NAME == 'dev') {
                        APP_PORT = '3001'
                        IMAGE_TAG = 'nodedev:v1.0'
                    } else {
                        error "Unknown branch ${env.BRANCH_NAME}"
                    }
                    echo "Branch: ${env.BRANCH_NAME}, Port: ${APP_PORT}, Image: ${IMAGE_TAG}"
                }
            }
        }

        stage('Build') {
            steps {
                echo "Building project..."
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image ${IMAGE_TAG}..."
                sh "docker build -t ${IMAGE_TAG} ."
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying branch ${env.BRANCH_NAME} on port ${APP_PORT}..."
               
                sh "docker rm -f ${IMAGE_TAG} || true"
                
                sh "docker run -d -p ${APP_PORT}:3000 --name myapp-${env.BRANCH_NAME} ${IMAGE_TAG}"
            }
        }
    }
    post {
        success {
            echo "Deployment of ${env.BRANCH_NAME} successful!"
        }
        failure {
            echo "Deployment of ${env.BRANCH_NAME} failed!"
        }
    }
}
