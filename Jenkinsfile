pipeline {
    agent any

    environment {
        // Docker Hub credentials will be injected here
        DOCKER_CREDENTIALS = 'dockerhub-creds'
        IMAGE_NAME = 'mitali23/mywebapp'
        IMAGE_TAG = '1.0'
    }

    stages {

        stage('Checkout SCM') {
            steps {
                git(
                    url: 'https://github.com/me-mitali/MyWebApp.git',
                    branch: 'master',
                    credentialsId: '' // leave empty if public repo
                )
            }
        }

        stage('Build with Maven') {
            steps {
                echo 'Building application with Maven...'
                bat 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                bat "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_CREDENTIALS}",
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat "docker login -u %DOCKER_USER% -p %DOCKER_PASS%"
                    bat "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }

        stage('Deploy Container') {
            steps {
                echo 'Deploying container...'
                // Example: run container on the Jenkins host
                bat "docker stop mywebapp || exit 0"
                bat "docker rm mywebapp || exit 0"
                bat "docker run -d --name mywebapp -p 8080:8080 ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }

    }

    post {
        success {
            echo '✅ Pipeline succeeded!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}

        
