pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "meiviezhidocker/trend-app"
        EKS_CLUSTER = "trend-eks-cluster"
        AWS_REGION = "us-east-1"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Validate Files') {
            steps {
                sh '''
                    echo "Checking project files..."
                    test -f Dockerfile
                    test -f k8s/deployment.yaml
                    test -f k8s/service.yaml
                    echo "Required files are present."
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .
                    docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest
                '''
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
                        docker push ${DOCKER_IMAGE}:latest
                        docker logout
                    '''
                }
            }
        }

        stage('Configure EKS') {
            steps {
                sh '''
                    aws eks update-kubeconfig \
                        --region ${AWS_REGION} \
                        --name ${EKS_CLUSTER}

                    kubectl get nodes
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml

                    kubectl set image deployment/trend-app \
                        trend-app=${DOCKER_IMAGE}:${BUILD_NUMBER}

                    kubectl rollout status deployment/trend-app
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    echo "=== Deployment ==="
                    kubectl get deployment trend-app

                    echo "=== Pods ==="
                    kubectl get pods -l app=trend-app

                    echo "=== Service ==="
                    kubectl get service trend-app-service
                '''
            }
        }
    }

    post {
        success {
            echo 'Trend application deployed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the Jenkins console output.'
        }
    }
}