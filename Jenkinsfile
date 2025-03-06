pipeline {
    agent any

    environment {
        RESOURCE_GROUP = "nbaksclust-rg"
        AKS_CLUSTER = "prod-aks"
        BRANCH_NAME = "main"
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    echo "Building branch: ${BRANCH_NAME}"
                    checkout scm
                }
            }
        }

        stage('Azure Login') {
            steps {
                script {
                    withCredentials([azureServicePrincipal('Azure_SP_ID')]) {
                        sh '''
                        az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID
                        az aks get-credentials --resource-group $RESOURCE_GROUP --name $AKS_CLUSTER --overwrite-existing
                        '''
                    }
                }
            }
        }

        // stage('Build & Push Docker Image') {
        //     steps {
        //         script {
        //             sh "az acr login --name $ACR_NAME"
        //             sh "docker build -t $ACR_LOGIN_SERVER/$IMAGE_NAME:$IMAGE_TAG ."
        //             sh "docker push $ACR_LOGIN_SERVER/$IMAGE_NAME:$IMAGE_TAG"
        //         }
        //     }
        // }

        stage('Deploy to AKS') {
            when {
                expression { BRANCH_NAME == 'main' || BRANCH_NAME == '' }
            }
            steps {
                script {
                    def envNamespace = BRANCH_NAME == 'main' ? "main" : "staging"
                    sh "kubectl apply -f ./prometheusmont/"
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    sh "kubectl get pods -n ${BRANCH_NAME == 'main' ? 'default' : 'jenkins'}"
                    sh "kubectl get svc -n ${BRANCH_NAME == 'main' ? 'default' : 'jenkins'}"
                }
            }
        }
    }
}
