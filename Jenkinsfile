pipeline {
    agent any

tools {
        git 'Default Git'  // The name must exactly match what you entered in Global Tool Configuration
    }
    stages {
        // ... your stages ...
    }

    environment {
        // Docker Hub username (write your own)
        DOCKERHUB_USER = "oluwaseun2424"
        // The name of the image on Docker Hub
        IMAGE_NAME = "${DOCKERHUB_USER}/my-app"cle
        // The credential ID created in Jenkins
        DOCKER_CRED_ID = "dockerhub+credentials"
        // Give the image a unique tag, usually using the build` number
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Login to Docker Hub') {
            steps {
                // Use credentials to securely log in to Docker Hub
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CRED_ID}", 
                                                  usernameVariable: 'DOCKER_USER', 
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    // Note: Use double quotes to ensure variables are parsed
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build the image and tag it with both the build number and latest
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Push Docker Image') {
            steps {
                // Push both tags to Docker Hub
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker push ${IMAGE_NAME}:latest"
            }
        }
    }

    post {
        always {
            // Good habit: Log out and clean up local images after pipeline ends
            sh 'docker logout'
            sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest || true"
        }
    }
}