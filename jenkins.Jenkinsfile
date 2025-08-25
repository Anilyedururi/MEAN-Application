pipeline {
    agent any
    environment {
        DH_USER = 'anil1129'            
        DH_TOKEN = 'dckr_pat_JLhgEkOQl4NL7DuJXPhjjuona4Q'  
        FRONTEND_IMAGE = "${DH_USER}/frontapp"
        BACKEND_IMAGE = "${DH_USER}/myapp"
        VM_SSH_KEY = 'ubuntu (aws-key)'         
        VM_USER = 'ubuntu'                                 
        VM_HOST = '52.70.128.109'                       
    }    
    stages {
        stage ("git checkout"){
            steps{
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/Anilyedururi/Mean.git'
            }
        }
        stage ('build frontend image'){
            steps{
                script{
                    sh """
                        echo "$DH_TOKEN" | docker login -u "$DH_USER" --password-stdin
                        docker build -t $FRONTEND_IMAGE:latest ./frontend
                        docker push $FRONTEND_IMAGE:latest
                    """
                }
            }
        }
        stage ('build backend image'){
            steps{
                script{
                    sh """
                        echo "$DH_TOKEN" | docker login -u "$DH_USER" --password-stdin
                        docker build -t $BACKEND_IMAGE:latest ./frontend
                        docker push $BACKEND_IMAGE:latest
                    """
                }
            }
        }
        stage('Deploy to VM') {
            steps {
                script {
                    sh """
                        echo "$VM_SSH_KEY" > key.pem
                        chmod 600 key.pem
                        ssh -o StrictHostKeyChecking=no -i key.pem ${VM_USER}@${VM_HOST} '
                            cd ~/apps/my-app/deploy &&
                            docker compose pull &&
                            docker compose up -d &&
                            docker image prune -f
                        '
                        
                    """
                }
            }
        }
    }
    post {
        always {
            sh 'docker logout || true'
            sh 'rm -f key.pem || true'
        }
    }
}
