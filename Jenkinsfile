pipeline {
    agent any

    environment {
        IMAGE_NAME = "hello-devops"
        IMAGE_TAG  = "v${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Cloning repository...'
                checkout scm
            }
        }

        stage('Build Docker image') {
            steps {
                echo "Building image ${IMAGE_NAME}:${IMAGE_TAG}"
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh '''
                    docker run --rm ${IMAGE_NAME}:${IMAGE_TAG} \
                    python -c "import app; print('App imported OK')"
                '''
            }
        }

        stage('Push image') {
            steps {
                echo 'In a real pipeline this would push to ECR or DockerHub'
                sh 'docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Updating Kubernetes deployment...'
                sh 'kubectl set image deployment/hello-devops hello-devops=${IMAGE_NAME}:${IMAGE_TAG}'
            }
        }

    }

    post {
        success {
            echo 'Pipeline succeeded! App is deployed.'
        }
        failure {
            echo 'Pipeline failed. Check the logs above.'
        }
    }
}
