pipeline {
    agent any
    environment {
        DOCKER_PASSWORD = credentials('docker-hub-credentials') // Jenkins credentials ID
    }
    stages {
        stage('Checkout Code') {
            steps {
                // Checkout the code from GitHub
                git branch: 'master', url: 'https://github.com/raoadi20/demoproject.git'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                // Install dependencies using npm
                sh 'npm install'
            }
        }
        
        stage('Unit Test') {
            steps {
                // Run unit tests using npm
                sh 'npm test'
            }
        }
        
        stage('Login to Docker Hub') {
            steps {
                script {
                    // Login to Docker Hub
                    sh 'echo $DOCKER_PASSWORD | docker login -u raoadi20 --password-stdin'
                }
            }
        }
        
        stage('Staging Deployment') {
            steps {
                script {
                    // Staging on Development Server
                    // Stop any existing container
                    sh 'docker stop nodejs-staging || true && docker rm nodejs-staging || true'
                    // Run the Docker container in detached mode
                    sh 'docker run -d --name nodejs-staging -p 3000:3000 nodejs-app'
                    
                    // Push the Docker image to Docker Hub
                    sh 'docker tag nodejs-app raoadi20/nodejs-app' // Tagging the image
                    sh 'docker push raoadi20/nodejs-app'           // Pushing the image to Docker Hub
                }
            }
        }
        
        stage('Deploy to Production') {
            steps {
                // Deploy to production server
                sshagent(['prod-ssh-key']) {
                    sh """
                    ssh prosecops@172.16.5.111 '
                    docker ps -a --format "{{.Names}}" | grep nodejs-prod || true && docker stop nodejs-prod || true && docker rm nodejs-prod || true
                    docker pull raoadi20/nodejs-app
                    docker run -d --name nodejs-prod -p 3000:3000 raoadi20/nodejs-app
                    '
                    """
                }
            }
        }
    }
}
