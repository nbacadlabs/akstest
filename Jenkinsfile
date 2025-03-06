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
      stage('Run Node Setup') {
        steps {
          container('node') {
            sh 'apk update'
            sh 'apk add --no-cache curl bash jq sudo py3-pip'
            sh 'python3 -m pip install --upgrade pip setuptools wheel'
            sh 'pip install azure-cli'

            // Verify Azure CLI installation
            sh 'az version'
            sh 'az account show'
          }
        }
      }
    }
}
