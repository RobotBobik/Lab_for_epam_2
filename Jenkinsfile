pipeline {
    agent any
    parameters {
        choice(name: 'BRANCH', choices: ['main', 'dev'], description: 'Choose branch to deploy')
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: "${params.BRANCH}", url: 'https://github.com/RobotBobik/Lab_for_epam_2.git' // Replace with your repo URL
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
                    sh "sudo -S docker build -t ${imageName} ."
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    def port = params.BRANCH == 'main' ? '3000' : '3001'
                    def imageName = params.BRANCH == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    sh 'sudo -S docker stop $(docker ps -a -q)'
                    sh 'sudo -S docker rm $(docker ps -a -q)'
                    sh 'sudo -S docker run -d --expose ${port} -p ${port}:${port} ${imageName}'
                }
            }
        }
    }
}
