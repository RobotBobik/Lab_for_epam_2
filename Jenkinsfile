
pipeline {
    agent any
    environment {
        PORT = BRANCH_NAME == 'main' ? '3000' : '3001'
        IMAGE_TAG = BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
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
                    sh "docker build -t ${IMAGE_TAG} ."
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    // Видалення попередніх контейнерів для поточного середовища
                    sh """
                    docker ps -q --filter "name=${BRANCH_NAME}" | xargs -r docker stop
                    docker ps -aq --filter "name=${BRANCH_NAME}" | xargs -r docker rm
                    """
                    // Запуск нового контейнера
                    sh "docker run -d --name ${BRANCH_NAME} --expose ${PORT} -p ${PORT}:${PORT} ${IMAGE_TAG}"
                }
            }
        }
    }
}
