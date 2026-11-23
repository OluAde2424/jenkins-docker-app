pipeline {
    agent any

    environment {
        // Docker Hub username
        DOCKERHUB_USER = "oluwaseun2424"
        // The name of the image on Docker Hub
        IMAGE_NAME = "${DOCKERHUB_USER}/my-app"
        // Make sure this matches the ID in Jenkins exactly (using a hyphen)
        DOCKER_CRED_ID = "dockerhub-credentials " 
        // Give the image a unique tag using the build number
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CRED_ID}", 
                                                  usernameVariable: 'DOCKER_USER', 
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker push ${IMAGE_NAME}:latest"
            }
        }
    }

    post {
        always {
            sh 'docker logout'
            sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest || true"
        }
    }
}
