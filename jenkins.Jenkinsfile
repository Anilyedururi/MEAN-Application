pipeline {
    agent any
    environment {
        FRONTEND_IMAGE = "${DH_USER}/frontapp"
        BACKEND_IMAGE = "${DH_USER}/myapp"
    }    
    stages{
        stage("checkout"){
            steps{
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/Anilyedururi/Mean.git'
            }
        }
        stage("build image-f"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'Dockerhub', passwordVariable: 'docker_password', usernameVariable: 'docker_user')]) {
                    sh "docker build -t anil1129/frontapp:1.1 ./frontend "
                    sh "docker login -u ${docker_user} -p ${docker_password}"
                    sh  "docker push anil1129/frontapp:1.1"
                }
            }
        }
        stage("build image-b"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'Dockerhub', passwordVariable: 'docker_password', usernameVariable: 'docker_user')]) {
                    sh "docker build -t anil1129/backapp:1.2 ./backend" 
                    sh "docker login -u ${docker_user} -p ${docker_password}"
                    sh  "docker push anil1129/backapp:1.2"
                }
            }   
        }
        stage("deploy vm"){
            steps{
                sshagent(['AWS-cred']) {
                    sh"""
                        ssh -o StrictHostKeyChecking=no ubuntu@172.31.84.99 "
                            docker rm -f frontapp
                            mkdir -p ~/apps/my-app/deploy
                            cd ~/apps/my-app/deploy
                            docker run -d -p 80:80 anil1129/frontapp:1.1
                            docker-compose pull
                            docker-compose up -d
                            docker image prune -f
                        "
                    """
                }
            }
        }
    }
}
