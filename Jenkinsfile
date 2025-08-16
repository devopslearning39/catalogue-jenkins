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
                    PackageVersion = packageJson.version
                    echo "application version: ${PackageVersion}"
                }
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh '''
                    ls -la
                    zip -q -r catalogue.zip ./* -x "*.git*" -x "*.zip" -x "Jenkinsfile"
                    ls -ltr
                '''
            }
        }

        stage('Artifact upload to Nexus') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '13.217.13.39:8081',
                    groupId: 'com.roboshop',
                    version: "${PackageVersion}",
                    repository: 'catalogue',
                    credentialsId: 'nexus-auth',
                    artifacts: [[
                        artifactId: 'catalogue',
                        classifier: '',
                        file: 'catalogue.zip',
                        type: 'zip'
                    ]]
                )
            }
        }

        stage('Invoking deploy pipeline job') {
            steps {
                script {
                        def params = [
                            string(name: 'version', value: "$packageVersion"),
                            string(name: 'environment', value: "dev")
                        ]
                        build job: "catalogue-deploy", wait: true, parameters: params
                }
            }
        }
    }

    post {
        always {
            echo 'This will invoke all the time of Jenkins execution...'
            deleteDir()
        }
        failure {
            echo 'Jella, build got failed, Please check!'
        }
        success {
            echo 'Booom!!!, Jella build executed successfully.'
        }
    }
}