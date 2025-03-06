pipeline {
    agent {
        kubernetes {
          yaml '''
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: maven
                image: maven:alpine
                command:
                - cat
                tty: true
              - name: node
                image: node:16-alpine3.12
                command:
                - cat
                tty: true
            '''
        }
    }
    
    stages {
        stage('Run Azure CLI in Docker') {
            steps {
                script {
                    // Run Azure CLI commands inside the Azure CLI container
                    docker.image('mcr.microsoft.com/azure-cli').inside {
                        sh '''
                        # Login to Azure using Service Principal credentials
                        az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID
                        
                        # Get credentials for AKS cluster
                        az aks get-credentials --resource-group $RESOURCE_GROUP --name $AKS_CLUSTER --overwrite-existing
                        
                        # Run kubectl commands for deployment
                        kubectl get pods
                        kubectl get svc
                        '''
                    }
                }
            }
      }
      stage('Run maven') {
        steps {
        //   container('maven') {
        //     sh 'mvn -version'
        //     sh 'echo Hello > hello.txt'
        //     sh 'ls -last'
        //   }
          container('node') {
            sh 'npm version'
            // sh 'cat hello.txt'
            // sh 'ls -last'
            sh 'apk update'
            sh 'apk add --no-cache curl bash jq'
            sh 'apk add --no-cache curl'
            sh 'apk add --no-cache sudo'
            sh 'curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"'
            sh 'sudo chmod +x kubectl'
            sh 'mv kubectl /usr/local/bin/'
            sh 'kubectl version --client'
            sh 'apk add --no-cache jq'
            sh 'jq --version'
            sh 'apk add --no-cache bash'
            sh 'az login'
            
            sh '''
              sudo apt update && \
              sudo apt install -y azure-cli
            '''
            // sh 'apk add --no-cache apt'
          }
        }
      }
    }
}