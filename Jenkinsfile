pipeline {
    agent any
    parameters {
        choice(name: 'BRANCH', choices: ['main', 'dev'], description: 'Choose branch to deploy')
    }
    environment {
        DOCKER_PASSWORD = credentials('fed894be-232a-4e3f-a3d4-5316daf9623d') // Replace with your Docker Hub password credential ID
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
        stage('Build and Push Docker Image') {
            steps {
                script {
                    def imageName = "robotbobik/${params.BRANCH == 'main' ? 'nodemain' : 'nodedev'}:v1.0" // Include your Docker Hub username (salo)

                   withCredentials([string(credentialsId: '105b4042-4bd6-4305-8b99-e3d6bca3e72d', variable: 'DOCKER_PASS')]) {
                        sh 'docker build -t ${imageName} . --pull'
                        sh 'docker login -u robotbobik -p \$DOCKER_PASS'  // Docker Hub login
                        sh 'docker push ${imageName}'              // Push to Docker Hub
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    def port = params.BRANCH == 'main' ? '3000' : '3001'
                    def imageName = "salo/${params.BRANCH == 'main' ? 'nodemain' : 'nodedev'}:v1.0" // Use the same image name as in the build stage

                    sh 'docker stop $(docker ps -a -q -f name=${imageName}) || true'  // Stop existing container by name if it's running
                    sh 'docker rm $(docker ps -a -q -f name=${imageName}) || true'  // Remove existing container by name
                    sh 'docker run -d --rm --name ${imageName} -p ${port}:${port} ${imageName}'  // Run the new container
                }
            }
        }
    }
}