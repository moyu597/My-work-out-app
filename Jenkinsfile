pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub_id') // Docker Hub credentials
        FRONTEND_IMAGE = 'Dockerfile' // Name of your frontend image
        BACKEND_IMAGE = 'Dockerfile'// Name of your backend image
        FRONTEND_DIRECTORY = 'frontend' // Frontend directory
        BACKEND_DIRECTORY = 'backend' // Backend directory
        DOCKER_REGISTRY_URL = 'https://docker.io' // Your Docker registry URL
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout scm
                }
            }
        }

        // Add a new stage for MongoDB URI
        stage('Use MongoDB URI') {
            steps {
                script {
                    // Retrieve MongoDB URI from Jenkins Credentials
                    MONGO_URI = credentials('MongoDBURI')
                    // Use the MONGO_URI environment variable in your build steps
                    sh "echo MONGO_URI is $MONGO_URI"
                    // Add your MongoDB-related build steps here
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
                    // Use Docker Hub credentials stored in Jenkins Credentials
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-username', passwordVariable: 'DOCKER_HUB_CREDENTIALS')]) {
                        docker.withRegistry("${DOCKER_REGISTRY_URL}", "DOCKER_HUB_CREDENTIALS") {
                            docker.image("${FRONTEND_IMAGE}:${BUILD_NUMBER}").push()
                            docker.image("${BACKEND_IMAGE}:${BUILD_NUMBER}").push()
                        }
                    }
                }
            }
        }
    }
        

    post {
        always {
            script {
                docker.image("${FRONTEND_IMAGE}:${BUILD_NUMBER}").stop()
                docker.image("${FRONTEND_IMAGE}:${BUILD_NUMBER}").remove()
                docker.image("${BACKEND_IMAGE}:${BUILD_NUMBER}").stop()
                docker.image("${BACKEND_IMAGE}:${BUILD_NUMBER}").remove()
            }
        }
    }
}
