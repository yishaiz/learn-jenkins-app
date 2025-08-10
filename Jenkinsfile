
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
                    image 'mcr.microsoft.com/playwright:v1.54.0-noble'
                    reuseNode true
                    args '-u root' // Use root user to avoid permission issues
                }
            }
            steps {
                sh '''
                    npm install -g serve
                    serve -s build &
                    npx playwright test
                '''
            }
            // sleep 5
            // post {
            //     always {
            //         sh 'pkill -f serve || true'
            //     }
            }
        }
    }

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
