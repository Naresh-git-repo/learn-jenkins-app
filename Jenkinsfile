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
                # 1. Start the app in the background
                npx --yes serve -s build -l 3000 &
    
                # 2. Wait for the server to be alive
                npx --yes wait-on http://localhost:3000 --timeout 60000
    
                # 3. THE MISSING LINK: Tell Playwright to download its own browser
                # This fixes the "Executable doesn't exist" error
                npx playwright install chromium --with-deps
    
                # 4. Now run the tests
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
