pipeline {
    agent any

    environment {
        ACR_NAME = "akspracticeacr26abin"
        IMAGE_NAME = "fastapi-aks"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Clone Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $ACR_NAME.azurecr.io/$IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }

        stage('Login to ACR') {
            steps {
                sh '''
                az acr login --name $ACR_NAME
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                docker push $ACR_NAME.azurecr.io/$IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }

        stage('Deploy to AKS') {
            steps {
                sh '''
                sed -i "s|IMAGE_TAG|$IMAGE_TAG|g" deployment.yaml

                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }
}