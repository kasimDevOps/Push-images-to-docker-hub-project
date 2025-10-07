pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "your-dockerhub-username/python-flask-app"
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds') // Jenkins credentials ID
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo "Cloning code from GitHub..."
                git branch: 'main', url: 'https://github.com/your-repo/python-docker-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh 'docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .'
            }
        }

        stage('Login to DockerHub') {
            steps {
                echo "Logging into DockerHub..."
                sh 'echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin'
            }
        }

        stage('Tag and Push Image') {
            steps {
                echo "Tagging and pushing Docker image..."
                sh '''
                    docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest
                    docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
                    docker push ${DOCKER_IMAGE}:latest
                '''
            }
        }

        stage('Cleanup') {
            steps {
                echo "Cleaning up local images..."
                sh 'docker rmi ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest || true'
            }
        }
    }

    post {
        success {
            echo "✅ Image pushed successfully to DockerHub!"
        }
        failure {
            echo "❌ Build failed. Check Jenkins logs."
        }
    }
}
