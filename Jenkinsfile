pipeline {
    agent any

    environment {
        IMAGE_NAME = 'mitali23/mywebapp'
        IMAGE_TAG = '1.0'
        DOCKER_CREDENTIALS = 'dockerhub-creds' // Jenkins credential ID
    }

    stages {
        stage('Checkout SCM') {
            steps {
                echo 'Checking out source code...'
                git url: 'https://github.com/me-mitali/MyWebApp.git', branch: 'master'
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
        bat "docker build -t %IMAGE_NAME%:%IMAGE_TAG% ."

        // Get Git commit hash first
        script {
            def gitCommit = bat(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
            echo "Git commit hash: ${gitCommit}"

            // Tag the Docker image with commit hash
            bat "docker tag %IMAGE_NAME%:%IMAGE_TAG% %IMAGE_NAME%:${gitCommit}"
        }
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
                    bat 'echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin'
                    bat 'docker push %IMAGE_NAME%:%IMAGE_TAG%'
                    bat 'docker push %IMAGE_NAME%:%IMAGE_TAG_HASH%'
                }
            }
        }

        stage('Deploy Container (Optional)') {
            steps {
                echo 'Skipping deployment for now.'
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}


   
