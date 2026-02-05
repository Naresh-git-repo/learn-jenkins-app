pipeline {
    agent any

    stages {
        stage('Build') {
            agent{
                docker{
                image 'node:18-alpine'
                reuseNode true
            }
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
        stage('Test'){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                echo "Test stage"
                sh '''
                test -f build/index.html
                npm test
                '''   
            }
        }
        stage('E2E'){
            agent{
                docker{
                    image 'mcr.microsoft.com/playwright:v1.58.0-noble'
                    reuseNode true
                }
            }
            steps{
                echo "E2E stage"
                sh '''
                # 1. Install 'serve' locally so we are sure it exists in this container
                npm install serve &
                node_modules/.bin/serve -s build -l 3000
                npx wait-on http://localhost:3000 --timeout 30000
                npx playwright test
                '''   
            }
        }
    }
    options{
        timestamps()
    }
    post{
        always{
            junit 'jest-results/junit.xml'
        }
    }
}
