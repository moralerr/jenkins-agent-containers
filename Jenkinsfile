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
                            def changedFiles = []

                            if (currentBuild.changeSets) {
                                for (changeSet in currentBuild.changeSets) {
                                    for (item in changeSet.items) {
                                        // Get affected files for each commit
                                        def affectedFiles = item.affectedFiles

                                        // Filter files containing "Dockerfile" in the path
                                        def dockerFiles = affectedFiles.findAll { file -> file.path.contains("Dockerfile") }
                                        changedFiles.addAll(dockerFiles)
                                    }
                                }
                            }

                            if (changedFiles) {
                                // Login to Docker registry
                                sh 'echo "$DOCKER_PASSWORD" | docker login --username $DOCKER_USERNAME --password-stdin'
                                println "Building Image..."
                                for (file in changedFiles) {
                                    echo "Processing: $file.path"

                                    def imageName = file.path.split('/')[-2]

                                    sh "docker build -t ${imageName}:latest -f ${file.path} ."
                                    sh "docker tag ${imageName}:latest ${REGISTRY_NAME}/${REGISTRY_REPO}:jenkins-${imageName}-agent-${IMAGE_VERSION}"
                                    sh "docker push ${REGISTRY_NAME}/${REGISTRY_REPO}:jenkins-${imageName}-agent-${IMAGE_VERSION}"

                                }
                                // Logout of registry
                                sh "docker logout"
                            } else {
                                echo "No changes detected to files containing Dockerfile."
                            }
                        }
                    }
                }
            }
        }
    }
}
