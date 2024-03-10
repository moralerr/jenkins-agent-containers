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
                container("dind") {
                    withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {

                        script {

                            // Get the changed files from the previous build
                            def changes = currentBuild.changeSets

                            // Initialize an empty list to store paths of changed Dockerfiles
                            def dockerfiles = []

                            // Iterate over the changes and find Dockerfile paths
                            changes.affectedFiles.each { change ->
                                change.each { entry ->
                                    def path = entry.path
                                    if (path.contains("Dockerfile")) {
                                        dockerfiles.add(path)
                                    }
                                }
                            }

                            // Print out the paths of changed Dockerfiles
                            echo "Changed Dockerfiles:"
                            dockerfiles.each { dockerfile ->
                                echo "- ${dockerfile}"
                            }

                            // Login to Docker registry
                            sh 'echo "$DOCKER_PASSWORD" | docker login --username $DOCKER_USERNAME --password-stdin'

                            // Call the function to get the changed files
                            def changedFiles = getChangedFiles()

                            if (changedFiles) {

                                for (file in changedFiles) {

                                    echo "Processing: ${file.path}"

                                    // Your Docker image build, tag, and push commands here
                                    // Make sure to properly handle the 'file' object here
                                    // For example:
                                    /*
                                    def imageName = file.path.split('/')[-2]
                                    sh "docker build -t ${imageName}:latest -f ${file.path} ."
                                    sh "docker tag ${imageName}:latest ${REGISTRY_NAME}/${REGISTRY_REPO}:jenkins-${imageName}-agent-${IMAGE_VERSION}"
                                    sh "docker push ${REGISTRY_NAME}/${REGISTRY_REPO}:jenkins-${imageName}-agent-${IMAGE_VERSION}"
                                    */
                                }

                            } else {
                                echo "No changes detected between this build and the previous one."
                            }

                            sh 'docker logout'
                        }
                    }
                }
            }
        }
    }
}



def getChangedFiles() {
    def changedFiles = []

    if (currentBuild.changeSets) {
        currentBuild.changeSets.each { changeSet ->
            changeSet.items.each { item ->
                def affectedFiles = item.affectedFiles.collect {
                    [path: it.path, editType: it.editType] // Accessing editType directly
                }
                changedFiles.addAll(affectedFiles)
            }
        }
    }

    return changedFiles
}

