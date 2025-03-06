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
            sh '''
              apk update
              apk add --no-cache curl bash jq sudo py3-pip python3-dev gcc musl-dev libffi-dev
              python3 -m pip install --upgrade pip setuptools wheel
              pip install azure-cli
            '''

            // Verify Azure CLI installation
            sh 'az version'
            sh 'az account show'
          }
        }
      }
    }
}
