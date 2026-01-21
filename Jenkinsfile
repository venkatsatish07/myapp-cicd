pipeline {
    agent any

    environment {
        IMAGE_NAME = "venkats061/myapp"
        IMAGE_TAG  = "1.0"

        DOCKER_CREDS = credentials('dockerhub-creds')
<<<<<<< Updated upstream
        KUBECONFIG = "/var/lib/jenkins/.kube/config" // ✅ added
=======

        // Kubernetes access
        KUBECONFIG = "/var/lib/jenkins/.kube/config"
>>>>>>> Stashed changes
    }

    stages {

        stage('Checkout') {
            steps {
                echo '📥 Checking out source code...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                sh '''
                  docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo '📤 Pushing Docker image to Docker Hub...'
                sh '''
                  echo "$DOCKER_CREDS_PSW" | docker login -u "$DOCKER_CREDS_USR" --password-stdin
                  docker push ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Apply Sealed Secrets') {
            steps {
                echo '🔐 Applying SealedSecrets to Kubernetes...'
                sh '''
                  kubectl apply -f k8s/secrets/db-creds-sealed.yaml
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo '🚀 Deploying application to Kubernetes...'
                sh '''
                  # Use Jenkins kubeconfig explicitly
                  kubectl apply -f k8s/deployment.yaml
                  kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully'
        }
        failure {
            echo '❌ Pipeline failed'
        }
    }
}
