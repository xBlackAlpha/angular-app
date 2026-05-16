def getVersion(){
    def version = bat returnStdout: true, script: 'git rev-parse --short HEAD'
    return version.trim().readLines().last()
}

pipeline {
    agent any
    environment {
        DOCKER_TAG = getVersion()
    }
    stages {
        stage('Clone Stage') {
            steps {
                git 'https://github.com/xBlackAlpha/angular-app.git'
            }
        }
        stage('Docker Build') {
            steps {
                bat 'docker build -t salim178/angular-app:%DOCKER_TAG% .'
            }
        }
        stage('DockerHub Push') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'DockerHubPassword')]) {
                    bat 'docker login -u salim178 -p %DockerHubPassword%'
                }
                bat 'docker push salim178/angular-app:%DOCKER_TAG%'
            }
        }
        stage('Deploy via SSH') {
            steps {
                sshagent(credentials: ['vm-ssh-key']) {
                    bat """
                        ssh -o StrictHostKeyChecking=no jenkins@192.168.1.21 "docker pull salim178/angular-app:%DOCKER_TAG% && docker run -d -p 80:80 salim178/angular-app:%DOCKER_TAG%"
                    """
                }
            }
        }
    }
}