pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub_id') // Docker Hub credentials
        FRONTEND_IMAGE = 'my-workout-app-frontend' // Name of your frontend image
        BACKEND_IMAGE = 'my-workout-app-backend' // Name of your backend image
        FRONTEND_DIRECTORY = 'frontend' // Frontend directory
        BACKEND_DIRECTORY = 'backend' // Backend directory
        DOCKER_REGISTRY_URL = 'https://index.docker.io/v1/' // Your Docker registry URL
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout scm
                }
            }
        }

        stage('Build Backend') {
            steps {
                dir("${BACKEND_DIRECTORY}") {
                    script {
                        docker.build("${BACKEND_IMAGE}:${BUILD_NUMBER}", "-f Dockerfile .")
                    }
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir("${FRONTEND_DIRECTORY}") {
                    script {
                        docker.build("${FRONTEND_IMAGE}:${BUILD_NUMBER}", "-f Dockerfile .")
                    }
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub_id', passwordVariable: 'DOCKER_HUB_CREDENTIALS')]) {
                        docker.withRegistry("${DOCKER_REGISTRY_URL}", "DOCKER_HUB_CREDENTIALS") {
                            docker.image("${FRONTEND_IMAGE}:${BUILD_NUMBER}").push()
                            docker.image("${BACKEND_IMAGE}:${BUILD_NUMBER}").push()
                        }
                    }
                }
            }
        }

        stage('Run Backend Container') {
            steps {
                withCredentials([string(credentialsId: 'MongoDBURI', variable: 'MONGO_URI')]) {
                    sh "docker run -e MONGO_URI=$MONGO_URI -p 8080:8080 -d ${BACKEND_IMAGE}:${BUILD_NUMBER}"
                }
            }
        }
    }

    post {
        always {
            script {
                sh "docker rmi ${FRONTEND_IMAGE}:${BUILD_NUMBER}"
                sh "docker rmi ${BACKEND_IMAGE}:${BUILD_NUMBER}"
            }
        }
    }
}
