pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "srividyagajjala11/my-k8s-app"
        DOCKER_TAG = "v1"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/GajjalaSrividya/node-k8s-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }

        stage('Deploy to Minikube') {
    steps {
        script {
            // Optional: list files for debugging
            sh "ls -l k8s/"
            
            // Apply deployment and service separately
            sh "kubectl apply -f k8s/deployment.yaml"
            sh "kubectl apply -f k8s/service.yaml"
        }
    }
}
    }

    post {
        success {
            echo 'Pipeline completed successfully! Node.js app is deployed on Minikube.'
        }
        failure {
            echo 'Pipeline failed. Check logs for errors.'
        }
    }
}
