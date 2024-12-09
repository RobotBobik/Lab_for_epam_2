pipeline {
    agent any
    parameters {
        choice(name: 'BRANCH', choices: ['main', 'dev'], description: 'Choose branch to deploy')
    }
    environment {
        DOCKER_USERNAME = 'robotbobik'  // Your Docker Hub username
        IMAGE_NAME = "${DOCKER_USERNAME}/${params.BRANCH == 'main' ? 'nodemain' : 'nodedev'}:v1.0"
        PORT = params.BRANCH == 'main' ? '3000' : '3001'
        DOCKER_PASSWORD = credentials('fed894be-232a-4e3f-a3d4-5316daf9623d') // Or use a different credential ID

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
            agent { docker { image 'docker:latest' } }
            steps {

                sh 'docker build -t ${env.IMAGE_NAME} .'  // Build the Docker image
                withCredentials([string(credentialsId: '105b4042-4bd6-4305-8b99-e3d6bca3e72d', variable: 'DOCKER_PASS')]) {
                    sh 'docker login -u ${env.DOCKER_USERNAME} -p ${DOCKER_PASS}' // Log in to Docker Hub
                    sh 'docker push ${env.IMAGE_NAME}'              // Push to Docker Hub
                }
                // OR build and push with buildx (if available and preferred):
                // sh "docker buildx build --platform linux/amd64 -t ${env.IMAGE_NAME} --push . --tag ${env.IMAGE_NAME}"
            }
        }
        stage('Deploy') {
            agent { docker { image 'docker:latest' } }
            steps {
                def containerId = sh(script: "docker ps -a -q -f name=${env.IMAGE_NAME}", returnStdout: true).trim()
                sh 'docker stop ${containerId} || true'
                sh 'docker rm ${containerId} || true'
                sh 'docker run -d --rm --name ${env.IMAGE_NAME} -p ${env.PORT}:${env.PORT} ${env.IMAGE_NAME}'
            }
        }
    }
}