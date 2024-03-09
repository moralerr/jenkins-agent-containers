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
                            // Get the workspace directory
                            def workspace = pwd()

                            dir(workspace + '/*') {  // Enclose the for loop within the dir block
                                // Loop through subdirectories with Dockerfiles
                                for (folder in pwd()) {
                                    if (fileExists(folder + '/Dockerfile')) {
                                        // Get folder name
                                        def imageName = folder.split('/')[-1]

                                        // Build the image with tag
                                        docker.build(imageName: "jenkins-${imageName}-agent-${IMAGE_VERSION}", file: folder + '/Dockerfile')

                                        // Login to Docker registry
                                        sh 'echo "$DOCKER_PASSWORD" | docker login --username $DOCKER_USERNAME --password-stdin'

                                        // Push Image to Docker registry
                                        sh "docker push ${REGISTRY_NAME}/${REGISTRY_REPO}:${IMAGE_NAME}-${IMAGE_VERSION}"

                                        // Logout of registry
                                        sh "docker logout"
                                    }
                                }
                            }  // End of dir block


                        }
                    }
                }
            }
        }
    }
}
