pipeline {
    agent any
    environment {
        // Replace with your AWS ECR URL, e.g., 123456789012.dkr.ecr.us-west-2.amazonaws.com/apigateway
        ECR_URL = '217100784311.dkr.ecr.ap-southeast-1.amazonaws.com/frontend-repo'
        DOCKER_IMAGE = 'frontend'
        IMAGE_TAG = 'v1'
    }
    stages {
        stage('Checkout') {
            steps {
                // Pulls the API Gateway repository from GitHub
                git url: 'https://github.com/Runner-app-org/frontend.git', branch: 'master'
            }
        }
        stage('Build Docker Image') {
            steps {
                // Builds the Docker image with tag apigateway:v1
                sh 'docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} .'
            }
        }
        stage('Tag Docker Image') {
            steps {
                // Tags the image for AWS ECR
                sh 'docker tag ${DOCKER_IMAGE}:${IMAGE_TAG} ${ECR_URL}:${IMAGE_TAG}'
            }
        }
        // stage('Push to ECR') {
        //     steps {
        //         // Logs in to AWS ECR and pushes the image
        //         withAWS(credentials: 'aws-credentials-id', region: 'ap-southeast-1') {
        //             sh 'aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin ${ECR_URL}'
        //             sh 'docker push ${ECR_URL}:${IMAGE_TAG}'
        //         }
        //     }
        // }
        // stage('Deploy to Kubernetes') {
        //     steps {
        //         // Applies the Kubernetes deployment configuration
        //         sh 'kubectl apply -f deployment.yaml'
        //     }
        // }
    }
    post {
        always {
            // Clean up Docker images to save space
            sh 'docker rmi ${DOCKER_IMAGE}:${IMAGE_TAG} ${ECR_URL}:${IMAGE_TAG} || true'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}