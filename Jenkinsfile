pipeline {
    agent any
    parameters {
        choice(name: 'BRANCH', choices: ['main', 'dev'], description: 'Choose branch to deploy')
    }
    environment {
        DOCKER_PASSWORD = credentials('105b4042-4bd6-4305-8b99-e3d6bca3e72d')
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: "${params.BRANCH}", url: 'https://github.com/RobotBobik/Lab_for_epam_2.git'
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
                    withCredentials([string(credentialsId: '206abae6-7661-4d3f-9acf-25c7d85cd07f', variable: 'DOCKER_PASS')] ) {
                        sh 'docker build -t "${imageName}" .' // Single quotes, variable interpolated in groovy
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    def port = params.BRANCH == 'main' ? '3000' : '3001'
                    def imageName = params.BRANCH == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'

                    withCredentials([string(credentialsId: '105b4042-4bd6-4305-8b99-e3d6bca3e72d', variable: 'DOCKER_PASS')] ) {
                        sh 'docker stop $(docker ps -a -q) || true'
                        sh 'docker rm $(docker ps -a -q) || true'
                        sh "docker run -d --rm --name ${imageName} --expose ${port} -p ${port}:${port} ${imageName}" // Mixed for safety; imageName is already safe.
                    }
                }
            }
        }
    }
}