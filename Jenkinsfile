pipeline {
    agent any

    stages {
        stage('Build') {
            docker{
                image 'node:18-alpine'
                reuseNode true
            }
            steps {
                sh '''
                echo "Build stage..."
                ls -la
                npm --version
                node --version
                npm ci
                npm run build
                ls -la
                '''
            }
        }
    }
}
