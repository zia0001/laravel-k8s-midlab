pipeline {
    agent any

    environment {
        IMAGE_NAME = 'laravel-midlab:1.0'
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME} .'
            }
        }

        stage('Load Image into Minikube') {
            steps {
                sh 'minikube image load ${IMAGE_NAME}'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/mysql.yaml'
                sh 'kubectl rollout status deployment/mysql --timeout=180s'
                sh 'kubectl apply -f k8s/laravel.yaml'
                sh 'kubectl rollout status deployment/laravel --timeout=180s'
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl get pods -o wide'
                sh 'kubectl get services'
            }
        }
    }
}
