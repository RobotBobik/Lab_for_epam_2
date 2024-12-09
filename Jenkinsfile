pipeline {
    agent any
    parameters {
        choice(name: 'BRANCH', choices: ['main', 'dev'], description: 'Choose branch to deploy')
    }
    environment {
        DOCKER_PASSWORD = '1234' // Замінено credentials на пароль для тестування
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: '${params.BRANCH}', url: 'https://github.com/RobotBobik/Lab_for_epam_2.git'
            }
        }
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    def imageName = params.BRANCH == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    sh 'echo ${env.DOCKER_PASSWORD} | sudo -S docker build -t ${imageName} .'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    def port = params.BRANCH == 'main' ? '3000' : '3001'
                    def imageName = params.BRANCH == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    sh 'echo ${env.DOCKER_PASSWORD} | sudo -S docker stop $(docker ps -a -q) || true'
                    sh 'echo ${env.DOCKER_PASSWORD} | sudo -S docker rm $(docker ps -a -q) || true'
                    sh 'echo ${env.DOCKER_PASSWORD} | sudo -S docker run -d --expose ${port} -p ${port}:${port} ${imageName}'
                }
            }
        }
    }
}
