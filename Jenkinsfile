pipeline {
    agent {
        kubernetes {
            // Name of the predefined pod template in Jenkins
            label 'agent-pod'
            // Any additional container if needed
            defaultContainer 'jnlp'
        }
    }
    environment {
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'  // ID of the stored Docker Hub credentials
        DOCKER_REPO = 'arunareath/java-app'
    }
    stages {
        stage('Build') {
            steps {
                container('maven') {
                    script {
                        // Your build steps, e.g., Maven build commands
                        sh 'mvn clean install'
                    }
                }
            }
        }
        stage('Docker Login') {
            steps {
                container('docker') {
                    script {
                        withCredentials([usernamePassword(credentialsId: "${env.DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                            sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                        }
                    }
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                container('docker') {
                    script {
                        sh 'docker build -t ${DOCKER_REPO}:latest .'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                container('docker') {
                    script {
                        sh 'docker push ${DOCKER_REPO}:latest'
                    }
                }
            }
        }
    }
    post {
        always {
            container('docker') {
                script {
                    sh 'docker logout'
                }
            }
        }
        success {
            echo 'Docker image pushed successfully!'
        }
        failure {
            echo 'Failed to push Docker image.'
        }
    }
}
