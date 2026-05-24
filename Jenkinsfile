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

        stage('Azure Login') {
            steps {

                withCredentials([usernamePassword(
                    credentialsId: 'azure-sp',
                    usernameVariable: 'AZURE_CLIENT_ID',
                    passwordVariable: 'AZURE_CLIENT_SECRET'
                )]) {

                    sh '''
                    az login --service-principal \
                      --username $AZURE_CLIENT_ID \
                      --password $AZURE_CLIENT_SECRET \
                      --tenant f8cea540-60d7-4415-93b2-6dd05ecad6c0
                    '''
                }
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

        stage('Connect to AKS') {
                steps {
                    sh '''
                    az aks get-credentials \
                      --resource-group abindev-rg \
                      --name aks-practice-cluster \
                      --overwrite-existing
                    '''
                }
            }

        stage('Attach ACR to AKS') {
                steps {
                    sh '''
                    az aks update \
                      --resource-group abindev-rg \
                      --name aks-practice-cluster \
                      --attach-acr $ACR_NAME
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