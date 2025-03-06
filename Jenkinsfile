pipeline {
    agent any

    environment {
        RESOURCE_GROUP = "nbaksclust-rg"
        AKS_CLUSTER = "prod-aks"
        BRANCH_NAME = "main"
    }

    stages {
        stage('Install Azure CLI') {
            steps {
                script {
                    sh '''
                    if ! command -v az &> /dev/null
                    then
                        echo "Azure CLI not found, installing..."
                        curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
                    else
                        echo "Azure CLI already installed"
                    fi
                    '''
                }
            }
        }

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
                        sh 'echo connected'
                    }
                }
            }
        }

        // stage('Deploy to AKS') {
        //     when {
        //         expression { env.BRANCH_NAME && env.BRANCH_NAME == 'main' }
        //     }
        //     steps {
        //         script {
        //             def envNamespace = BRANCH_NAME == 'main' ? "main" : "staging"
        //             sh "kubectl apply -f ./prometheusmont/ -n ${envNamespace}"
        //         }
        //     }
        // }

        // stage('Verify Deployment') {
        //     steps {
        //         script {
        //             def envNamespace = BRANCH_NAME == 'main' ? "default" : "jenkins"
        //             sh "kubectl get pods -n ${envNamespace}"
        //             sh "kubectl get svc -n ${envNamespace}"
        //         }
        //     }
        // }
    }
}
