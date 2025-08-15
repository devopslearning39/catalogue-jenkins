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
        stage('Fetching the version number') {
            steps {
                script {
                def packageJson = readJSON file: 'package.json'
                env.PackageVersion = packageJson.version
                echo $PackageVersion
            }
          }
        }
    }
}