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
        stage('Tests'){
            parallel {
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
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            steps{
                echo "E2E stage"
                sh '''
                npm install serve
                node_modules/.bin/serve -s build &
                sleep 10
                npx playwright test --reporter=line,html
                ls -l playwright-report
                '''   
            }
        }
    }

            }
        }
        stage('Deploy') {
            agent{
                docker{
                image 'node:18-alpine'
                reuseNode true
            }
            }
            steps {
                sh '''
                npm install netlify-cli
                node_modules/.bin/netlify--version
                '''
            }
        }
        
    options{
        timestamps()
    }
    post{
        always{
            junit 'jest-results/junit.xml'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}
