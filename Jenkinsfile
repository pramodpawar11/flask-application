pipeline {
    agent any
    stages{
        stage("Checkout"){
            steps{
                git url: "https://github.com/pramodpawar11/flask-application.git", branch:"master"
            }
        }
        stage("Build"){
            steps{
                sh "docker build -f ./DockerMultistage -t flask-application:v3 ."
            }
        }
        stage("Manual Testing"){
            steps{
                echo "Developer / Tester tests likh ke dega..."
            }
        }
        stage("Push to dockerhub"){
            steps{
                withCredentials([usernamePassword(credentialsId:"dockerhubCred",usernameVariable:"dockerhubUsername",passwordVariable:"dockerhubPassword")]){
                sh "docker login -u ${env.dockerhubUsername} -p ${env.dockerhubPassword}"
                sh "docker image tag flask-application:v3 ${env.dockerhubUsername}/flask-application:v3"
                sh "docker push ${env.dockerhubUsername}/flask-application:v3"
                }
            }
        }
        stage("Start the application"){
            steps{
                sh "docker compose down"
                sh "docker compose pull"
                sh "docker compose up -d"
            }
        }
    }
}


