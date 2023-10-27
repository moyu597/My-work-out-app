pipeline {
    agent any // This pipeline can be executed on any available agent

    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub_id' // Docker Hub credentials
        FRONTEND_DOCKER_IMAGE = 'my-workout-app-frontend' // Name of your frontend image
        BACKEND_DOCKER_IMAGE = 'my-workout-app-backend' // Name of your backend image
    }

    stages {
        stage('Checkout') { // Stage for checking out the source code from the SCM
            steps {
                script {
                    checkout scm // Check out the source code from the configured SCM
                }
            }
        }

        stage('Build backend') { // Stage for building the backend
            steps {
                withCredentials([string(credentialsId: 'MongoDBURI', variable: 'MONGODB_URI')]) {
                    dir('backend') { // Change to 'backend' directory
                        sh '''
                            npm install // Install npm dependencies for the backend
                            echo MongoDBURI=$MONGODB_URI // Export the MongoDB URI as an environment variable
                        '''
                    }
                }
            }
        }

        stage('Build Frontend') { // Stage for building the frontend
            steps {
                dir('frontend') { // Change to 'frontend' directory
                    sh '''
                        npm install // Install npm dependencies for the frontend
                    '''
                }
            }
        }

        stage('Build Images') { // Stage for building Docker images
            steps {
                script {
                    dockerImageBackend = docker.build("${BACKEND_DOCKER_IMAGE}:$BUILD_NUMBER", './backend') // Build Docker image for the backend and tag it with build number
                    dockerImageFrontend = docker.build("${FRONTEND_DOCKER_IMAGE}:$BUILD_NUMBER", './frontend') // Build Docker image for the frontend and tag it with build number
                }
            }
        }

        stage('Push Images to DockerHub') { // Stage for pushing Docker images to DockerHub
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) { // Retrieve DockerHub credentials from Jenkins credential store
                    script {
                        sh '''
                            echo "${DOCKER_PASSWORD}" | docker login -u "${DOCKER_USERNAME}" --password-stdin // Login to DockerHub using the retrieved credentials

                            docker tag ${BACKEND_DOCKER_IMAGE}:$BUILD_NUMBER ${BACKEND_DOCKER_IMAGE}:$BUILD_NUMBER 
                            docker push ${BACKEND_DOCKER_IMAGE}:$BUILD_NUMBER 

                            docker tag ${FRONTEND_DOCKER_IMAGE}:$BUILD_NUMBER ${FRONTEND_DOCKER_IMAGE}:$BUILD_NUMBER 
                            docker push ${FRONTEND_DOCKER_IMAGE}:$BUILD_NUMBER 

                            docker tag ${BACKEND_DOCKER_IMAGE}:$BUILD_NUMBER ${BACKEND_DOCKER_IMAGE}:latest 
                            docker push ${BACKEND_DOCKER_IMAGE}:latest 

                            docker tag ${FRONTEND_DOCKER_IMAGE}:$BUILD_NUMBER ${FRONTEND_DOCKER_IMAGE}:latest 
                            docker push ${FRONTEND_DOCKER_IMAGE}:latest 
                        '''
                    }
                }
            }
        } 
}

}