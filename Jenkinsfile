pipeline {
    agent any

    stages {

        /*
        stage('Build') {
            agent {
                docker{
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
                    ls -ls
                '''
            }
        }
        */

        stage('Test') {
            agent {
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    test -f /build/index.html
                    npm test
                '''
            }
        }
         stage('E2E') {
            agent {
                docker{
                    image 'mcr.microsoft.com/playwright:v1.62.0-noble'
                    reuseNode true

                }
            }
            steps {
                sh '''
                    npm install serve
                    serve -s build
                    sleep 10
                    npx playwright test
                '''
            }
        }
    }

    post {
        always{
            junit 'jest-results/junit.xml'
        }
    }
}
