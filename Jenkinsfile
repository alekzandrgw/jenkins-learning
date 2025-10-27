pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '299e0267-d4c2-41f1-afa1-b06f51687819'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

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
                    ls -lah
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -lah
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

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-u root'
                }
            }
            steps {
                sh '''
                npm install netlify-cli -g
                netlify --version
                echo 'Deploying to Netlify... with site ID: ' $NETLIFY_SITE_ID
                netlify status
                netlify deploy '--dir=build' --prod
                echo 'Deployment complete.'
                '''
            }
        }
    }

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }   
}