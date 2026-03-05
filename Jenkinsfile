pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                } 
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                } 
            }
            
            steps {
                sh '''
                    test -f build/index.html
                    npm test
                '''
            }
        }

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy' //Playwright image with Node 18  
                    reuseNode true
                    // args '-u root:root' // Run as root to avoid permission issues with Playwright
                } 
            }
            
            steps {
                sh '''
                    #npm install -g serve
                    npm install serve
                    #serve -s build
                    node_modules/.bin/serve -s build & 
                    sleep 10
                    npx playwright test --reporter=html
                '''
            }
        }
    }

    post {
        always {
            junit 'jest-results/junit.xml'
        }
    }
}