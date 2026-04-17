pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                git branch: 'main', url: 'https://github.com/anantshrivastava26/vle7.git'
            }
        }
        
        stage('Build & Test Docker Image') {
            steps {
                echo 'Building Docker Image: vle7-app...'
                sh 'docker build -t vle7-app:latest .'
                sh 'MINIKUBE_HOME=/home/ubuntu minikube image load vle7-app:latest'
                echo 'Docker build complete!'
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying application to Minikube...'
                // Using minikube's built-in kubectl
                sh 'KUBECONFIG=/home/ubuntu/.kube/config kubectl apply -f deployment.yaml'
                sh 'KUBECONFIG=/home/ubuntu/.kube/config kubectl rollout restart deployment vle7-app'
                echo 'Deployment applied successfully!'
            }
        }
    }
}
