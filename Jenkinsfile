pipeline {
    agent {
        kubernetes {
            label agent-pod
        } 
    }
    environment {
        DOCKER_USERNAME = 'arunareath'
        DOCKER_PASSWORD = '^runfive5App'
    }
    stages {
        stage('Clone') {
            steps {
                container('maven') {
                    checkout scm
                    sh 'mvn package'
                }
            }
        }
        stage('Build-Docker-Image') {
            steps {
                container('docker') {
                    sh 'docker build -t arunareath/java-app:latest .'
               }
            }
        }
        stage('Login-Into-Docker') {
            steps {
                container('docker') {
                     sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                }
            }
        }
        stage('Push-Images-Docker-to-DockerHub') {
            steps {
                container('docker') {
                     sh 'docker push arunareath/java-app:latest'
                }
            }
        }
    }
    post {
        always {
            container('docker') {
                sh 'docker logout'
            }
        }
    }
}
