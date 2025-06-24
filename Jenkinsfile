pipeline {
    agent any

    environment {
        IMAGE_NAME = 'sulavkumar/helloworldtestimage'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        DOCKER_CREDENTIALS = credentials('dockerhub-creds')  // Jenkins credentials ID
    }

    stages {
        stage('Clone from GitHub') {
            steps {
                git url: 'https://github.com/sulavkumar/jenkinstest.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building image: ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }
        
        stage('Scanning Image') {
        steps {
            sysdigImageScan engineCredentialsId: 'sysdig-secure-api-credentials', imageName: "${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                    echo $DOCKER_CREDENTIALS_PSW | docker login -u $DOCKER_CREDENTIALS_USR --password-stdin
                    docker push $IMAGE_NAME:$IMAGE_TAG
                    docker logout
                '''
            }
        }
    }

    post {
        success {
            echo "Docker image pushed: $IMAGE_NAME:$IMAGE_TAG"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
