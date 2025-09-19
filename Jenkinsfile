pipeline {
    agent any
    environment {
        APP_PORT = ''
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout the current branch
                checkout scm
                script {
                    // Set port based on branch
                    if (env.BRANCH_NAME == 'main') {
                        APP_PORT = '3000'
                    } else if (env.BRANCH_NAME == 'dev') {
                        APP_PORT = '3001'
                    } else {
                        error "Unknown branch ${env.BRANCH_NAME}"
                    }
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
                echo "Building Docker image..."
                sh "docker build -t myapp:${env.BRANCH_NAME} ."
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying branch ${env.BRANCH_NAME} on port ${APP_PORT}..."
                // Remove any existing container
                sh "docker rm -f myapp-${env.BRANCH_NAME} || true"
                // Run new container
                sh "docker run -d -p ${APP_PORT}:3000 --name myapp-${env.BRANCH_NAME} myapp:${env.BRANCH_NAME}"
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
