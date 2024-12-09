pipeline {
    agent any
    parameters {
        choice(name: 'BRANCH', choices: ['main', 'dev'], description: 'Choose branch to deploy')
    }
    environment {
        DOCKER_USERNAME = 'robotbobik' // Docker Hub username
        IMAGE_NAME = "${DOCKER_USERNAME}/${params.BRANCH == 'main' ? 'nodemain' : 'nodedev'}:v1.0"
        PORT = "${params.BRANCH == 'main' ? '3000' : '3001'}" // Значення в лапках
    }
    stages {
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
            steps {
                script {
                    withCredentials([string(credentialsId: '105b4042-4bd6-4305-8b99-e3d6bca3e72d', variable: 'DOCKER_PASS')]) {
                        sh '''
                            docker build -t ${IMAGE_NAME} .
                            echo ${DOCKER_PASS} | docker login -u ${DOCKER_USERNAME} --password-stdin
                            docker push ${IMAGE_NAME}
                        '''
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    def containerName = "${params.BRANCH == 'main' ? 'nodemain' : 'nodedev'}"
                    def containerId = sh(script: "docker ps -a -q -f name=${containerName}", returnStdout: true).trim()
                    sh """
                        if [ -n "${containerId}" ]; then
                            docker stop ${containerId}
                            docker rm ${containerId}
                        fi
                        docker run -d --rm --name ${containerName} -p ${env.PORT}:${env.PORT} ${env.IMAGE_NAME}
                    """
                }
            }
        }
    }
}
