pipeline {
    agent any

    environment {
        // Replace with your Docker Hub username and desired image name
        DOCKER_HUB_USER = oluwaseun2424
        IMAGE_NAME      = "${DOCKER_HUB_USER}/my-app"
        IMAGE_TAG       = "${BUILD_NUMBER}"
        // ID of the credentials you create in Step 2
        DOCKER_CREDS    = docker-hub-credentials
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
                // Optional: Tag as latest if on main branch
                sh 'docker tag $IMAGE_NAME:$IMAGE_TAG $IMAGE_NAME:latest'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: DOCKER_CREDS,
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    // Using password-stdin is more secure than -p
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
                    // Push latest tag as well (optional)
                    sh 'docker push $IMAGE_NAME:latest'
                }
            }
        }
    }

    post {
        always {
            // Clean up local images to save disk space on the agent
            sh 'docker rmi $IMAGE_NAME:$IMAGE_TAG || true'
            sh 'docker rmi $IMAGE_NAME:latest || true'
            // Log out to clear credentials
            sh 'docker logout || true'
        }
    }
}
