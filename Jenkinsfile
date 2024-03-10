@Library('pipeline-commons') _
pipeline {
    agent {
        kubernetes {
            inheritFrom 'default'
        }
    }
    triggers {
        cron('H H * * *') // Build all images on every cron run
    }
    options {
        disableConcurrentBuilds()
    }
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
                            // Check if it's a cron trigger
                            def isCronJob = Cause.currentCause instanceof hudson.triggers.TimerTriggerCause

                            def changedFiles = sh(returnStdout: true, script: 'git diff --name-only --cached')
                            def hasDockerfileChanges = changedFiles.trim().contains('Dockerfile')

                            if (isCronJob || hasDockerfileChanges) {
                                def files = findFiles(glob: '**/Dockerfile')

                                // Login to Docker registry
                                sh 'echo "$DOCKER_PASSWORD" | docker login --username $DOCKER_USERNAME --password-stdin'

                                for( folder in files) {
                                    def imageName = folder.path.split('/')[-2]
                                    def buildImage = isCronJob || hasDockerfileChanges.contains(folder.path)

                                    if (buildImage) {
                                        sh "docker build -t ${imageName}:latest -f ${folder.path} ."
                                        sh "docker tag ${imageName}:latest ${REGISTRY_NAME}/${REGISTRY_REPO}:jenkins-${imageName}-agent-${IMAGE_VERSION}"
                                        sh "docker push ${REGISTRY_NAME}/${REGISTRY_REPO}:jenkins-${imageName}-agent-${IMAGE_VERSION}"
                                    }
                                }

                                // Logout of registry
                                sh "docker logout"
                            } else {
                                echo "No Dockerfile changes detected, skipping build."
                            }
                        }
                    }
                }
            }
        }
    }
}
