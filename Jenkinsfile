pipeline {
//      agent {
//             docker {
//                 image 'maven:3.9.7-eclipse-temurin-21-alpine'
//             }
//         }
    agent any
    tools {
        maven 'Maven3'
        dockerTool 'Docker' // Use the Docker tool configured in Jenkinss

    }
    environment {
        DOCKER_IMAGE = 'wxesquevixos/authentication-services'
        DOCKER_TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                git branch: 'main', url: 'https://github.com/wandersonxs/authentication-services.git'
            }
        }
        stage('Build') {
            steps {
                echo 'Building the application...'
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'mvn test'
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh """
                docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                """
            }
        }
        stage('Push Docker Image') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        sh """
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                        """
                    }
                }
            }
        }
        stage('Deploy to Minikube') {
            steps {
                echo 'Deploying to Minikube...'
                sh """
                kubectl apply -f kubernetes/authentication-services-deployment.yaml
                kubectl apply -f kubernetes/authentication-services-service.yaml
                kubectl apply -f kubernetes/authentication-services-ingress.yaml
                """
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
    }
}