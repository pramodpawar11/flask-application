pipeline {
    agent any
    
    environment {
        IMAGE_NAME = "pramodpawar11/flask-application"
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/pramodpawar11/flask-application.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Scan Docker Image with Trivy') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    sh "trivy image --severity CRITICAL --exit-code 0 ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }

        stage('Login and Push to Dockerhub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerHubCredentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }

        stage('Start the application') {
            steps {
               sh "IMAGE_TAG=${BUILD_NUMBER} docker compose down"
               sh "IMAGE_TAG=${BUILD_NUMBER} docker compose up -d"
            }
        }
    }
}
