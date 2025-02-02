@Library('pipeline-commons') _

pipeline {
    agent{
        docker {
            image 'moralerr/examples:jenkins-admin-agent-latest'
            label 'standalone'
        }
    }
    stages {
        stage('Check Versions') {
            steps {
                script {
                    sh '''
                        echo "Ansible version:"
                        ansible --version

                        echo "Git version:"
                        git --version

                        echo "jq version:"
                        jq --version

                        echo "LSB Release Info:"
                        lsb_release -a

                        echo "curl version:"
                        curl --version

                        echo "GPG version (from gnupg):"
                        gpg --version

                        echo "unzip version:"
                        unzip -v

                        echo "pip3 version:"
                        pip3 --version

                        echo "terraform version:"
                        terraform --version

                        echo "terragrunt version:"
                        terragrunt --version
                    '''
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
