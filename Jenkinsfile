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
        timeout(time: 30, unit: 'MINUTES')
    }
    environment {
        // Define environment variables
        IMAGE_VERSION = 'latest'
        REGISTRY_NAME = 'moralerr'
        REGISTRY_REPO = 'examples'
    }
    parameters {
        booleanParam(name: 'MAVEN', defaultValue: false, description: 'Build the Maven agent image')
        booleanParam(name: 'NODEJS', defaultValue: false, description: 'Build the NodeJS agent image')
    // Add more boolean params dynamically later
    }
    stages {
        stage('Build/Push Image') {
            steps {
                container('dind') {
                    withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        script {
                            // Directly check if the build was triggered by cron or not
                            def buildCause = currentBuild.rawBuild.getCauses()[0].toString()
                            echo "Build cause: ${buildCause}"

                            def buildTriggeredByCron = buildCause.contains('TimerTriggerCause')
                            def changedFiles = getChangedFiles()

                            def filesToProcess = []

                            // Handle cron builds
                            if (buildTriggeredByCron) {
                                echo 'Build triggered by cron job. Processing all Dockerfiles.'
                                filesToProcess = findAllDockerfiles('agents')
                            } else {
                                // Check for any boolean params that are selected
                                params.each { paramName, paramValue ->
                                    if (paramValue && paramName.toUpperCase() == paramName) {
                                        def lowerCaseParam = paramName.toLowerCase()
                                        def dockerfilePath = "agents/${lowerCaseParam}/Dockerfile"
                                        echo "Building image for ${paramName} using path ${dockerfilePath}"
                                        filesToProcess.add([path: dockerfilePath])
                                    }
                                }

                                // If no boolean params were selected, check for changed files
                                if (!filesToProcess && changedFiles) {
                                    echo 'Build triggered by changes in Dockerfiles.'
                                    filesToProcess = changedFiles
                                } else if (!filesToProcess) {
                                    echo 'No Dockerfile changes detected and no boolean params selected.'
                                }
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

// Function to get changed Dockerfiles and ignore other files like Jenkinsfile
def getChangedFiles() {
    def changedFiles = []

    if (currentBuild.changeSets) {
        currentBuild.changeSets.each { changeSet ->
            changeSet.items.each { item ->
                def affectedFiles = item.affectedFiles.findAll {
                    it.path.contains('Dockerfile') && !it.path.contains('Jenkinsfile')
                }.collect { [path: it.path] }
                changedFiles.addAll(affectedFiles)
                }
            }
        }

    return changedFiles.unique()
    }
