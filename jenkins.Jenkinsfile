pipeline {
    agent any
    environment {
        DH_USER = "anil1129"               // Jenkins Credentials (username)
        DH_TOKEN = "dckr_pat_BAaS91_s-yE7vl2oFpHTjPKShbw"
        FRONTEND_IMAGE = "${DH_USER}/my-frontend"
        BACKEND_IMAGE = "${DH_USER}/my-backend"
      VM_SSH_KEY = credentials('aws')       // SSH private key
        VM_USER = "anil"                            // VM SSH username (add this explicitly)
        VM_HOST = "13.222.222.128"                       // VM IP or hostname (add this explicitly)
    }
    stages {
        stage('Build & Push Frontend') {
            steps {
                script {
                    sh """
                        echo "$DH_TOKEN" | docker login -u "$DH_USER" --password-stdin
                        docker build -t $FRONTEND_IMAGE:latest ./frontend
                        docker push $FRONTEND_IMAGE:latest
                    """
                }
            }
        }
        stage('Build & Push Backend') {
            steps {
                script {
                    sh """
                        echo "$DH_TOKEN" | docker login -u "$DH_USER" --password-stdin
                        docker build -t $BACKEND_IMAGE:latest ./backend
                        docker push $BACKEND_IMAGE:latest
                    """
                }
            }
        }
        stage('Deploy to VM') {
            steps {
                script {
                    sh """
echo "$VM_SSH_KEY" > Discover-Dollor.pem
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
}
