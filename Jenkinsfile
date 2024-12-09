pipeline {
    agent any
    parameters {
        choice(name: 'BRANCH', choices: ['main', 'dev'], description: 'Choose branch to deploy')
    }
    environment {
        DOCKER_PASSWORD = credentials('206abae6-7661-4d3f-9acf-25c7d85cd07f')
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    try {
                        git branch: "${params.BRANCH}", url: 'https://github.com/RobotBobik/Lab_for_epam_2.git'
                    } catch (Exception e) {
                        error 'Failed to checkout branch ${params.BRANCH}. Verify the branch exists in the repository.'
                    }
                }
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
                    sh 'echo "${env.DOCKER_PASSWORD}" | sudo -S docker build -t ${imageName} .'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    def port = params.BRANCH == 'main' ? '3000' : '3001'
                    def imageName = params.BRANCH == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'

                    sh 'echo "${env.DOCKER_PASSWORD}" | sudo -S docker stop $(docker ps -a -q) || true'
                    sh 'echo "${env.DOCKER_PASSWORD}" | sudo -S docker rm $(docker ps -a -q) || true'
                    sh 'echo "${env.DOCKER_PASSWORD}" | sudo -S docker run -d --name ${imageName} --expose ${port} -p ${port}:${port} ${imageName}'
                }
            }
        }
    }
}
