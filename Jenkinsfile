//az aks get-credentials --resource-group $RESOURCE_GROUP --name $AKS_CLUSTER --overwrite-existing
pipeline {
    agent {
        kubernetes {
          yaml '''
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: node
                image: node:16-alpine3.12
                command:
                - cat
                tty: true
            '''
        }
    }

    stages {
      stage('Setup and Install Azure CLI') {
        steps {
          container('node') {
            // Verify Azure CLI installation
            sh 'az version'
          }
        }
      }
    }
}