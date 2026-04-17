pipeline {
    agent any
    
    environment {
        IMAGE_NAME = "vle7-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                git branch: 'main', url: 'https://github.com/anantshrivastava26/vle7.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo "Building Docker image ${IMAGE_NAME}:${IMAGE_TAG}..."
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }
        
        stage('Load Image Into Minikube') {
            steps {
                echo "Loading image ${IMAGE_NAME}:${IMAGE_TAG} into Minikube..."
                sh 'sudo -u ec2-user /usr/bin/env MINIKUBE_HOME=/home/ec2-user /usr/local/bin/minikube image load ${IMAGE_NAME}:${IMAGE_TAG}'
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                echo 'Applying Kubernetes manifests...'
                sh 'sudo -u ec2-user /usr/bin/env KUBECONFIG=/home/ec2-user/.kube/config /usr/local/bin/kubectl apply -f deployment.yaml'
                
                echo "Updating deployment image to ${IMAGE_NAME}:${IMAGE_TAG}..."
                sh 'sudo -u ec2-user /usr/bin/env KUBECONFIG=/home/ec2-user/.kube/config /usr/local/bin/kubectl set image deployment/vle7-app vle7-app=${IMAGE_NAME}:${IMAGE_TAG}'
                
                echo 'Waiting for rollout to finish...'
                sh 'sudo -u ec2-user /usr/bin/env KUBECONFIG=/home/ec2-user/.kube/config /usr/local/bin/kubectl rollout status deployment/vle7-app'
            }
        }
    }
}
