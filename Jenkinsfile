pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "mitali23/mywebapp:1.0"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'master', url: 'https://github.com/me-mitali/MyWebApp.git'
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
                bat "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Push to Docker Hub') {
    steps {
        echo 'Pushing Docker image to Docker Hub...'
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
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
                bat "docker rm -f mywebapp || echo 'No old container'"
                bat "docker run -d -p 8083:8080 --name mywebapp ${DOCKER_IMAGE}"
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Build failed!'
        }
    }
}
