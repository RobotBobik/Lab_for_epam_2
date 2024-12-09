pipeline {
    agent any
    parameters {
        choice(name: 'BRANCH', choices: ['main', 'dev'], description: 'Choose branch to deploy')
    }
    environment {
        DOCKER_USERNAME = 'robotbobik'
        IMAGE_NAME = ''  // Значення буде встановлено в скрипті
    }
    stages {
        stage('Set Environment Variables') {
            steps {
                script {
                    env.IMAGE_NAME = "${DOCKER_USERNAME}/${params.BRANCH == 'main' ? 'nodemain' : 'nodedev'}:v1.0"
                    env.PORT = params.BRANCH == 'main' ? '3000' : '3001'
                }
            }
        }
        stage('Checkout') {
            steps {
                git branch: "${params.BRANCH}", url: 'https://github.com/RobotBobik/Lab_for_epam_2.git'
            }
        }
        stage('Build App') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        stage('Build and Push Docker Image') {
            agent { docker { image 'docker:latest' } }
            steps {
                withCredentials([string(credentialsId: '105b4042-4bd6-4305-8b99-e3d6bca3e72d', variable: 'DOCKER_PASS')]) {
                    sh "docker build -t ${env.IMAGE_NAME} ."
                    sh "docker login -u ${env.DOCKER_USERNAME} -p ${DOCKER_PASS}"
                    sh "docker push ${env.IMAGE_NAME}"
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    def containerId = sh(script: "docker ps -a -q -f name=${env.IMAGE_NAME}", returnStdout: true).trim()
                    sh "docker stop ${containerId} || true"
                    sh "docker rm ${containerId} || true"
                    sh "docker run -d --rm --name ${env.IMAGE_NAME} -p ${env.PORT}:${env.PORT} ${env.IMAGE_NAME}"
                }
            }
        }
    }
}
