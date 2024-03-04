@Library('pipeline-commons') _
pipeline {
    agent {
        kubernetes {
            inheritFrom 'default'
        }
    }
    triggers {
        cron('H H * * *')
    }
    options { disableConcurrentBuilds() }
    environment {
        // Define environment variables
        IMAGE_NAME = 'jenkins-nodejs-agent'
        IMAGE_VERSION = 'latest'
        REGISTRY_NAME = 'moralerr'
        REGISTRY_REPO = 'examples'

    }
    stages {
        stage('Build/Push Image') {
            steps {
                container("dind"){
                    withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD' )]) {

                        script {
                            // Login to Docker registry
                            sh 'echo "$DOCKER_PASSWORD" | docker login --username $DOCKER_USERNAME --password-stdin'
                            // Build Docker image
                            sh "docker build -t ${IMAGE_NAME}:latest agents/nodejs"
                            sh "docker tag ${IMAGE_NAME}:latest ${REGISTRY_NAME}/${REGISTRY_REPO}:${IMAGE_NAME}-${IMAGE_VERSION}"
                            sh "docker push ${REGISTRY_NAME}/${REGISTRY_REPO}:${IMAGE_NAME}-${IMAGE_VERSION}"
                            sh "docker logout"
                        }
                    }
                }
            }
        }
    }
}
