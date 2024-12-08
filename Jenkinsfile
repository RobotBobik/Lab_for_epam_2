pipeline {
    agent any
    environment {
        PORT = "${BRANCH_NAME == 'main' ? '3000' : '3001'}"
        E_TAG = "${BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'}"
    }
    stages {
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
                sh "docker build -t ${E_TAG} ."
            }
        }
        stage('Deploy') {
            steps {
                sh "docker stop $(docker ps -q --filter 'ancestor=${E_TAG}') || true"
                sh "docker run -d -p ${PORT}:${PORT} ${E_TAG}"
            }
        }
    }
}
