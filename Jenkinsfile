node {

    def IMAGE_BACKEND  = "heeeyram/mean-backend:latest"
    def IMAGE_FRONTEND = "heeeyram/mean-frontend:latest"
    def VM_IP          = "15.206.66.162"

    stage('Clone Repository') {
        git branch: 'main',
            url: 'https://github.com/heeeyram/mean-crud-devops-assignment.git'
    }

    stage('Build Docker Images') {
        sh "docker build -t ${IMAGE_BACKEND} ./backend"
        sh "docker build -t ${IMAGE_FRONTEND} ./frontend"
    }

    stage('Push Images to DockerHub') {
        withCredentials([usernamePassword(
                credentialsId: 'dockerhub-creds',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
        )]) {
            sh '''
                echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                docker push heeeyram/mean-backend:latest
                docker push heeeyram/mean-frontend:latest
                docker logout
            '''
        }
    }

    stage('Deploy to VM using Docker Compose') {
        sshagent(['vm-ssh']) {
            sh """
                ssh -o StrictHostKeyChecking=no ubuntu@${VM_IP} '
                    cd mean-crud-devops-assignment &&
                    docker compose pull &&
                    docker compose down &&
                    docker compose up -d --remove-orphans
                '
            """
        }
    }

    stage('Success') {
        echo "Deployment Completed Successfully 🚀"
    }
}
