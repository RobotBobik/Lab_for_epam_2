pipeline {
    agent any
    parameters {
        choice(name: 'ENV', choices: ['main', 'dev'], description: 'Choose environment to deploy')
    }
    environment {
        DOCKER_USERNAME = 'robotbobik' 
        IMAGE_NAME = "${DOCKER_USERNAME}/${params.ENV == 'main' ? 'nodemain' : 'nodedev'}:v1.0"
        PORT = "${params.ENV == 'main' ? '3000' : '3001'}"
    }
    stages {
        stage('Pull Docker Image') {
            steps {
                script {
                    sh "docker pull ${env.IMAGE_NAME}"
                }
            }
        }
        stage('Stop and Remove Existing Container') {
            steps {
                script {
                    def containerName = "${params.ENV == 'main' ? 'nodemain' : 'nodedev'}"
                    def containerId = sh(script: "docker ps -a -q -f name=${containerName}", returnStdout: true).trim()
                    if (containerId) {
                        sh "docker stop ${containerId}"
                        sh "docker rm ${containerId}"
                    }
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    def containerName = "${params.ENV == 'main' ? 'nodemain' : 'nodedev'}"
                    sh "docker run -d --rm --name ${containerName} -p ${env.PORT}:${env.PORT} ${env.IMAGE_NAME}"
                }
            }
        }
    }
}