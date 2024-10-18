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
                container("dind") {
                    withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {

                        script {
                            def buildTriggeredByCron = currentBuild.getBuildCauses('org.jenkinsci.plugins.workflow.job.properties.PipelineTriggersJobProperty$CronTrigger') != null
                            def changedFiles = getChangedFiles()

                            if (changedFiles || buildTriggeredByCron) {
                                // Login to Docker registry
                                sh 'echo "$DOCKER_PASSWORD" | docker login --username $DOCKER_USERNAME --password-stdin'

                                def filesToProcess = changedFiles ?: [[path: 'Dockerfile']]
                                
                                for (file in filesToProcess) {
                                    def imageName = file.path.split('/')[-2]
                                    echo "Processing: ${file.path}"

                                    // Docker image build, tag, and push commands
                                    sh "docker build -t ${imageName}:latest -f ${file.path} ."
                                    sh "docker tag ${imageName}:latest ${REGISTRY_NAME}/${REGISTRY_REPO}:jenkins-${imageName}-agent-${IMAGE_VERSION}"
                                    sh "docker push ${REGISTRY_NAME}/${REGISTRY_REPO}:jenkins-${imageName}-agent-${IMAGE_VERSION}"
                                }

                                sh 'docker logout'

                            } else {
                                echo "No changes detected between this build and the previous one."
                            }
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
                def affectedFiles = item.affectedFiles.findAll { it.path.contains("Dockerfile") }.collect {
                    [path: it.path]
                }
                changedFiles.addAll(affectedFiles)
            }
        }
    }

    return changedFiles.unique()
}
