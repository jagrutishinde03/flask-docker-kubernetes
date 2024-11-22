pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "jagruti03shinde/web-app:latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Building the Docker image
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'c96d8628-a2c5-498b-9b36-6d592b25a6bf', 
                                                   usernameVariable: 'DOCKER_USER', 
                                                   passwordVariable: 'DOCKER_PASS')]) {
                    // Login to Docker Hub and push the image
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push ${DOCKER_IMAGE}
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
    steps {
        withCredentials([
            string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID')
        ]) {
            script {
                // Set AWS CLI credentials
                sh 'aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID'
                sh 'aws configure set region ap-south-1'

                // Run kubectl commands to deploy
                sh '''
                    export KUBECONFIG=~/.kube/config
                    kubectl apply -f k8s-deployment.yaml
                '''
            }
        }
    }
}

    }
}
