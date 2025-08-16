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
                    env.PackageVersion = packageJson.version
                    echo "application version: ${env.PackageVersion}"
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
                    version: "${env.PackageVersion}",
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
                    echo 'Triggering job for pipeline catalogue-deploy'
                    build job: 'catalogue-deploy',
                        wait: true,
                        parameters: [
                            string(name: 'version', value: "${env.PackageVersion}")
                        ]
                }
            }
        }
    }

    post {
        always {
            echo 'This will invoke all the time of Jenkins execution...'
            deleteDir() // Clean up to reduce workspace memory
        }
        failure {
            echo 'Jella, build got failed, please check!'
        }
        success {
            echo 'Booom!!! Jella build executed successfully.'
        }
    }
}
