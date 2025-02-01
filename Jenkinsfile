@Library('pipeline-commons') _

pipeline {
    docker {
        image 'moralerr/examples:jenkins-maven-agent-latest'
        label 'standalone'
    }
    stages {
        stage('Build/Push Image') {
            steps {
                script {
                    println 'Building image'
                }
            }
        }
    }
}
