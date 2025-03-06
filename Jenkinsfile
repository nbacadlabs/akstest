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
    environment {
        // Set any environment variables needed, if applicable
        PYTHON_ENV = 'python3'
    }
    
    stages {
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
            
            sh '''
              apk add py3-pip
              apk add gcc musl-dev python3-dev libffi-dev openssl-dev cargo make
              pip install --upgrade pip
              pip install azure-cli
            '''
            // sh 'apk add --no-cache apt'
          }
        }
      }
    }
}