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



                            // Call the function to get the changed files
                            def changedFiles = getChangedFiles()

                            def imageName = file.path.split('/')[-2]

                            def mavenParamBoolean = isParameterSetToTrue(imageName.toString().toUpperCase())
                            println mavenParamBoolean

                            if (changedFiles) {
                                // Login to Docker registry
                                sh 'echo "$DOCKER_PASSWORD" | docker login --username $DOCKER_USERNAME --password-stdin'

                                for (file in changedFiles) {

                                    echo "Processing: ${file.path}"

                                    // Your Docker image build, tag, and push commands here
                                    // Make sure to properly handle the 'file' object here
                                    // For example:
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

def isParameterSetToTrue(paramName) {
    return params.containsKey(paramName) && params.get(paramName).toString().equalsIgnoreCase("true")
}



