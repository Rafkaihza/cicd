pipeline {
    agent any

    environment {
        IMAGE_NAME = "rafkaihza78/bnsp-web"
        IMAGE_TAG  = "latest"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Login & Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credential',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push ${IMAGE_NAME}:${IMAGE_TAG}'
                }
            }
        }

        stage('Deploy Container') {
            steps {
                // stop container lama kalau ada, lalu run yang baru
                sh '''
                docker stop webapp || true
                docker rm webapp || true
                docker pull ${IMAGE_NAME}:${IMAGE_TAG}
                docker run -d -p 80:80 --name webapp ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }
    }
}
