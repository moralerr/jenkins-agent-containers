@Library('pipeline-commons') _

pipeline {
    agent{
        docker {
            image 'moralerr/examples:jenkins-maven-agent-latest'
            label 'standalone'
        }
    }
    stages {
        stage('Build/Push Image') {
            steps {
                script {
                    println 'Building image'
                    sh 'mvn --version'
                }
            }
        }
    }
    post{
        always {
            cleanWs()
        }
    }
}
