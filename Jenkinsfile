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

                            def files = findFiles(glob: '**/Dockerfile')
                            
                            if(files.length > 0) {
                                // Login to Docker registry
                                sh 'echo "$DOCKER_PASSWORD" | docker login --username $DOCKER_USERNAME --password-stdin'
                                for( folder in files) {
                                    def imageName = folder.path.split('/')[-2]
                                    // Build the image with tag
                                    docker.build(imageName: "jenkins-${imageName}-agent-${IMAGE_VERSION}", file: folder + '/Dockerfile')
                                    
                                    // Push Image to Docker registry
                                    sh "docker push ${REGISTRY_NAME}/${REGISTRY_REPO}:${IMAGE_NAME}-${IMAGE_VERSION}"
                                    
                                }
                                // Logout of registry
                                sh "docker logout"
                            }
                        }
                    }
                }
            }
        }
    }
}
