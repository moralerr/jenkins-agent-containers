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
    options {
        disableConcurrentBuilds()
    }
    environment {
        // Define environment variables
        IMAGE_VERSION = 'latest'
        REGISTRY_NAME = 'moralerr'
        REGISTRY_REPO = 'examples'
    }
    parameters {
        booleanParam(name: 'MAVEN', defaultValue: false, description: '')
        booleanParam(name: 'NODEJS', defaultValue: false, description: '')
    }
    stages {
        stage('Build/Push Image') {
            steps {
                container('dind') {
                    withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        script {
                            def buildTriggeredByCron = currentBuild.getBuildCauses('org.jenkinsci.plugins.workflow.job.properties.PipelineTriggersJobProperty$CronTrigger') != null
                            def changedFiles = getChangedFiles()

                            def filesToProcess = []

                            if (buildTriggeredByCron) {
                                echo 'Build triggered by cron job. Processing all Dockerfiles.'
                                // If build is triggered by cron, build all Dockerfiles under 'agents'
                                filesToProcess = findAllDockerfiles('agents')
                            } else if (changedFiles) {
                                echo 'Build triggered by changes in files.'
                                // If changes detected, only process the changed Dockerfiles
                                filesToProcess = changedFiles
                            }

                            if (filesToProcess) {
                                // Login to Docker registry
                                sh 'echo "$DOCKER_PASSWORD" | docker login --username $DOCKER_USERNAME --password-stdin'

                                for (file in filesToProcess) {
                                    def imageName
                                    def filePath = file.path
                                    def pathSegments = filePath.split('/')

                                    // Ensure we are in the correct directory for the Docker build
                                    if (!filePath.startsWith('agents/')) {
                                        filePath = "agents/${filePath}"
                                    }

                                    if (pathSegments.length > 1) {
                                        imageName = pathSegments[-2]  // Use the folder name as the image name
                                    } else {
                                        imageName = pathSegments[0]   // Fallback for unusual paths
                                    }

                                    echo "Processing: ${filePath}"

                                    // Docker image build, tag, and push commands
                                    sh "docker build -t ${imageName}:latest -f ${filePath} ${filePath.substring(0, filePath.lastIndexOf('/'))}"
                                    sh "docker tag ${imageName}:latest ${REGISTRY_NAME}/${REGISTRY_REPO}:jenkins-${imageName}-agent-${IMAGE_VERSION}"
                                    sh "docker push ${REGISTRY_NAME}/${REGISTRY_REPO}:jenkins-${imageName}-agent-${IMAGE_VERSION}"
                                }

                                sh 'docker logout'
                            } else {
                                echo 'No changes detected between this build and the previous one.'
                            }
                        }
                    }
                }
            }
        }
    }
}

// Function to find all Dockerfiles in the 'agents' directory
def findAllDockerfiles(String directory) {
    def dockerfiles = []
    dir(directory) {
        dockerfiles = findFiles(glob: '**/Dockerfile').collect { [path: it.path] }
    }
    return dockerfiles
}

def getChangedFiles() {
    def changedFiles = []

    if (currentBuild.changeSets) {
        currentBuild.changeSets.each { changeSet ->
            changeSet.items.each { item ->
                def affectedFiles = item.affectedFiles.findAll { it.path.contains('Dockerfile') }.collect {
                    [path: it.path]
            }
                changedFiles.addAll(affectedFiles)
        }
    }
}

    return changedFiles.unique()
}
