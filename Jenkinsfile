pipeline {
    agent any 
    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub_id' 
        FRONTEND_DOCKER_IMAGE = 'my-workout-app-frontend' 
        BACKEND_DOCKER_IMAGE = 'my-workout-app-backend'
    }

    stages {
        stage('Checkout') { 
            steps {
                script {
                    checkout scm 
                }
            }
        }

        stage('Build backend') { 
            steps {
                withCredentials([string(credentialsId: ' MongoDBURI', variable: 'MONGODB_URI')]) {
                    dir('backend') { 
                        sh '''
                            npm install #Install npm dependencies for the backend
                            echo MongoDBURI: ${MONGODB_URI} # Export the MongoDB URI as an environment variable
                        '''
                    }
                }
            }
        }

        stage('Build Frontend') { 
            steps {
                dir('frontend') { 
                    sh '''
                        npm install #Install npm dependencies for the frontend
                    '''
                }
            }
        }

        stage('Build Images') { 
            steps {
                script {
                    dockerImageBackend = docker.build("${BACKEND_DOCKER_IMAGE}:$BUILD_NUMBER", './backend') 
                    dockerImageFrontend = docker.build("${FRONTEND_DOCKER_IMAGE}:$BUILD_NUMBER", './frontend') 
                }
            }
        }

        stage('Push Images to DockerHub') { 
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) { 
                    script {
                        sh '''
                            echo "${DOCKER_PASSWORD}" | docker login -u "${DOCKER_USERNAME}" --password-stdin # Login to DockerHub using the retrieved credentials

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
