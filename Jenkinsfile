pipeline {
    agent any

    environment {
        // Replace with your Docker Hub Username and Repo Name
        DOCKER_HUB_USER = 'suhas50'
        IMAGE_NAME      = 'jenkins-docker-ci'
        IMAGE_TAG       = "${BUILD_NUMBER}"
        REGISTRY_CREDS  = 'Suhas@123'
    }

    stages {
        stage('Source Code Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Application Test') {
            steps {
                echo 'Running basic pre-build application verification...'
                sh 'node -v'
            }
        }

        stage('Docker Image Build') {
            steps {
                script {
                    echo "Building Docker image ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}..."
                    sh "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} ."
                    sh "docker tag ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Image Publishing') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${REGISTRY_CREDS}") {
                        echo "Pushing images to Docker Hub..."
                        sh "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
                        sh "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest"
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up local images...'
            sh "docker rmi ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} || true"
            sh "docker rmi ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest || true"
        }
        success {
            echo 'CI Pipeline executed successfully!'
        }
        failure {
            echo 'CI Pipeline failed.'
        }
    }
}
