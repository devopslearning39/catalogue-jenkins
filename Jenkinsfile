pipeline {
    agent {
        node {
            label 'AGENT-1'
        }
    }

    environment {
        PackageVersion = ''
    }

    options {
        ansiColor('xterm')
    }
    stages {
        stage('Get the version') {
            steps {
                script {
                    def packageJson = readJSON file: 'package.json'
                    packageVersion = packageJson.version
                    echo "application version: $packageVersion"
                }
            }
        }
         stage('Build') {
            steps {
                sh """
                    ls -la
                    zip -q -r catalogue.zip ./* -x "*.git" -x "*.zip" -x "Jenkinsfile"
                    ls -ltr
                """
            }
        }
    }

    post { 
        always { 
            echo 'This will invoke all the time of jenkins execution..'
            deleteDir()
        }
        failure { 
            echo 'I will always say Hello again!'
        }
        success { 
            echo 'This will invoke script executed successfully..'
        }
    }
}