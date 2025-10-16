pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/me-mitali/MyWebApp.git'
            }
        }

        stage('Build with Maven') {
            steps {
                echo "Building application with Maven..."
                bat 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                bat 'docker build -t mitali23/mywebapp:1.0 .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                                 usernameVariable: 'DOCKER_USER',
                                                 passwordVariable: 'DOCKER_PASS')]) {
                    bat """
                        echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                        docker push mitali23/mywebapp:1.0
                    """
                }
            }
        }

        stage('Deploy Container') {
            steps {
                echo "Deploying container..."
                bat 'docker run -d -p 8080:8080 mitali23/mywebapp:1.0'
            }
        }
    }

    post {
        success {
            echo "✅ Build and deployment successful!"
        }
        failure {
            echo "❌ Build failed!"
        }
    }
}
