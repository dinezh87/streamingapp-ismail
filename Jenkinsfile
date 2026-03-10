pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS'
    }
    
    environment {
        AWS_REGION = 'us-west-1'
        ECR_REGISTRY = '975050024946.dkr.ecr.us-west-1.amazonaws.com'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'YOUR_GIT_REPO_URL_HERE'
            }
        }
        
        stage('Install Dependencies') {
            parallel {
                stage('Auth Service') {
                    steps {
                        dir('backend/authService') {
                            nodejs(nodeJSInstallationName: 'NodeJS') {
                                sh 'npm install'
                            }
                        }
                    }
                }
                stage('Streaming Service') {
                    steps {
                        dir('backend/streamingService') {
                            nodejs(nodeJSInstallationName: 'NodeJS') {
                                sh 'npm install'
                            }
                        }
                    }
                }
                stage('Admin Service') {
                    steps {
                        dir('backend/adminService') {
                            nodejs(nodeJSInstallationName: 'NodeJS') {
                                sh 'npm install'
                            }
                        }
                    }
                }
                stage('Chat Service') {
                    steps {
                        dir('backend/chatService') {
                            nodejs(nodeJSInstallationName: 'NodeJS') {
                                sh 'npm install'
                            }
                        }
                    }
                }
                stage('Frontend') {
                    steps {
                        dir('frontend') {
                            nodejs(nodeJSInstallationName: 'NodeJS') {
                                sh 'npm install'
                            }
                        }
                    }
                }
            }
        }
        
        stage('Build Docker Images') {
            steps {
                script {
                    docker.build("streaming-auth:${BUILD_NUMBER}", "./backend/authService")
                    docker.build("streaming-service:${BUILD_NUMBER}", "./backend/streamingService")
                    docker.build("streaming-admin:${BUILD_NUMBER}", "./backend/adminService")
                    docker.build("streaming-chat:${BUILD_NUMBER}", "./backend/chatService")
                    docker.build("streaming-frontend:${BUILD_NUMBER}", "./frontend")
                }
            }
        }
        
        stage('Push to ECR') {
            steps {
                script {
                    docker.withRegistry("https://${ECR_REGISTRY}", "ecr:${AWS_REGION}:aws-credentials-dinesh") {
                        docker.image("dineshb14-treaming-auth:${BUILD_NUMBER}").push()
                        docker.image("dineshb14-streaming-service:${BUILD_NUMBER}").push()
                        docker.image("dineshb14-streaming-admin:${BUILD_NUMBER}").push()
                        docker.image("dineshb14-streaming-chat:${BUILD_NUMBER}").push()
                        docker.image("dineshb14-streaming-frontend:${BUILD_NUMBER}").push()
                    }
                }
            }
        }
    }
    
    post {
        success {
            echo 'CI Pipeline completed successfully!'
        }
        failure {
            echo 'CI Pipeline failed!'
        }
    }
}
