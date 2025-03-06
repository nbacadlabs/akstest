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
      stage('Run maven') {
        steps {
          container('maven') {
            sh 'mvn -version'
            sh 'echo Hello > hello.txt'
            sh 'ls -last'
          }
          container('node') {
            sh 'npm version'
            sh 'cat hello.txt'
            sh 'ls -last'
            sh 'sudo apt install curl'
            sh 'curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"'
            sh 'chmod +x kubectl'
            sh 'sudo mv kubectl /usr/local/bin/'
            sh 'kubectl version --client'
            sh 'sudo apt-get install jq'
            sh 'curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash'
          }
        }
      }
    }
}