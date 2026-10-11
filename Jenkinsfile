pipeline {
    agent any
      environment {
        DOCKER_IMAGE = 'akshu20791/apachewebsite:latest'
      //  KUBECONFIG = credentials('kubeconfig')
    }

    stages {
        stage('Clone Git repository') {
            steps {
                 git 'https://github.com/akshu20791/apachewebsite/'
            }
        }
        stage('run ansibleplaybook'){
          steps{
            ansiblePlaybook credentialsId: '3448c81c-b0d3-4ebe-bbdd-d4a95fcf8c38', installation: 'ansible2', inventory: 'inventory.ini', playbook: 'installapche.yml', vaultTmpPath: ''
          }
        }
       stage('Docker Build & Push') {
        steps {
            script {
                withDockerRegistry([credentialsId: 'docker']) {
                    sh '''
                    echo "Building Docker image..."
                    docker build --no-cache -t $DOCKER_IMAGE -f Dockerfile .
                    echo "Pushing Docker image to Docker Hub..."
                    docker push $DOCKER_IMAGE
                    '''
                    }
                }
            }
        }
       
        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([
                    file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_FILE')
                ]) {
                    sh '''
                        export KUBECONFIG="$KUBECONFIG_FILE"
                        kubectl apply -f deployment.yml
                        kubectl apply -f service.yml
                        kubectl get pods -o wide
                        kubectl get svc
                    '''
                }
            }
        }
 
    }
}




