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
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_FILE')]) {
                    script {
                        // Apply Kubernetes deployment and service files
                        sh '''
                            export KUBECONFIG=$KUBECONFIG_FILE
                            kubectl apply -f k8s-deployment.yaml
                            kubectl apply -f k8s-service.yaml
                        '''
                    }
                }
            }
        }
    }
}
