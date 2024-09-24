pipeline {
    agent any
    
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
        
        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image for the Node.js app
                    docker.build('nodejs-app')
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
                }
            }
        }
        
        stage('Deploy to Production') {
            steps {
                // Deploy to production server
                sshagent(['prod-ssh-key']) {
                    sh """
                    ssh prosecops@172.16.5.111 'docker stop nodejs-prod || true && docker rm nodejs-prod || true'
                    ssh prosecops@172.16.5.111 'docker pull nodejs-app'
                    ssh prosecops@172.16.5.111 'docker run -d --name nodejs-prod -p 3000:3000 nodejs-app'
                    """
                }
            }
        }
    }
}
