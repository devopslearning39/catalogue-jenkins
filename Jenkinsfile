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
        stage('Install dependencies') {
            steps {
                sh """
                    npm install
                """
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
        stage('Artifact upload to Nexus') {
            steps {
                nexusArtifactUploader(
                nexusVersion: 'nexus3',
                protocol: 'http',
                nexusUrl: '13.217.13.39:8081',
                groupId: 'com.roboshop',
                version: "${packageVersion}",
                repository: 'catalogue',
                credentialsId: 'nexus-auth',
                artifacts: [
                    [artifactId: 'catalogue',
                    classifier: '',
                    file: "catalogue.zip",
                    type: 'zip']
                ]
              )
            }
        }
    }

    post {
        always { 
            echo 'This will invoke all the time of jenkins execution..'
            deleteDir()     //This used to delete the zip file in node agent once the artifact uploaded to into Nexus repo (To reduce the memory in node)
        }
        failure { 
            echo 'Jella, build got failed, Please check!'
        }
        success { 
            echo 'Booom!!!, Jella build executed successfully.'
        }
    }
}