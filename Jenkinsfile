def getVersion(){
    def version = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return version.trim()
}

pipeline {
    agent any
    environment {
        DOCKER_TAG = getVersion()
    }
    stages {
        stage('Clone Stage') {
            steps {
                git 'https://gitlab.com/jmlhmd/datacamp_docker_angular.git'
            }
        }
        stage('Docker Build') {
            steps {
                sh 'docker build -t salim178/angular-app:${DOCKER_TAG} .'
            }
        }
        stage('DockerHub Push') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'DockerHubPassword')]) {
                    sh 'docker login -u salim178 -p ${DockerHubPassword}'
                }
                sh 'docker push salim178/angular-app:${DOCKER_TAG}'
            }
        }
        stage('Deploy via SSH') {
            steps {
                sshagent(credentials: ['vm-ssh-key']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no jenkins@192.168.1.21 \
                        'docker pull salim178/angular-app:${DOCKER_TAG} && \
                         docker run -d -p 80:80 salim178/angular-app:${DOCKER_TAG}'
                    """
                }
            }
        }
    }
}