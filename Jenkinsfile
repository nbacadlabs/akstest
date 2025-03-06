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
              # Function to check if a package is installed
              is_installed() {
                command -v "$1" >/dev/null 2>&1
              }

              echo "Updating package lists..."
              apk update

              # Install required dependencies only if missing
              for pkg in curl bash jq sudo py3-pip python3-dev gcc musl-dev libffi-dev openssl-dev cargo; do
                if ! apk info -e "$pkg" >/dev/null 2>&1; then
                  echo "Installing missing package: $pkg"
                  apk add --no-cache "$pkg"
                else
                  echo "$pkg is already installed."
                fi
              done

              # Ensure Cargo is in PATH
              export PATH="$HOME/.cargo/bin:$PATH"

              # Check and install Rust and Cargo if missing
              if ! is_installed cargo; then
                echo "Installing Rust and Cargo..."
                curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
                source "$HOME/.cargo/env"
              else
                echo "Rust and Cargo are already installed."
              fi

              # Verify Rust and Cargo installation
              rustc --version
              cargo --version

              # Check and install Azure CLI if missing
              if ! is_installed az; then
                echo "Azure CLI not found. Installing..."
                python3 -m pip install --upgrade pip setuptools wheel
                pip install azure-cli
              else
                echo "Azure CLI is already installed."
              fi

              # Verify Azure CLI installation
              az version
            '''
          }
        }
      }
      stage('Azure Login') {
            steps {
                script {
                    withCredentials([azureServicePrincipal('azure-credentials')]) {
                        sh '''
                        az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID
                        az show account
                        '''
                    }
                }
            }
        }
    }
}

//az aks get-credentials --resource-group $RESOURCE_GROUP --name $AKS_CLUSTER --overwrite-existing
//------OLD PIPELINEFILE---------
// pipeline {
//     agent {
//         kubernetes {
//           yaml '''
//             apiVersion: v1
//             kind: Pod
//             spec:
//               containers:
//               - name: node
//                 image: node:16-alpine3.12
//                 command:
//                 - cat
//                 tty: true
//             '''
//         }
//     }

//     stages {
//       stage('Setup and Install Azure CLI') {
//         steps {
//           container('node') {
//             sh '''
//               # Update package lists
//               apk update
              
//               # Install required dependencies
//               apk add --no-cache curl bash jq sudo py3-pip python3-dev gcc musl-dev libffi-dev openssl-dev cargo

//               # Install Rust and Cargo
//               curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
              
//               # Ensure Cargo is in PATH
//               export PATH="$HOME/.cargo/bin:$PATH"

//               # Verify Rust and Cargo installation
//               rustc --version
//               cargo --version

//               # Upgrade pip and install Azure CLI
//               python3 -m pip install --upgrade pip setuptools wheel
//               pip install azure-cli
//             '''

//             // Verify Azure CLI installation
//             sh 'az version'
//             sh 'az account show'
//           }
//         }
//       }
//     }
// }
