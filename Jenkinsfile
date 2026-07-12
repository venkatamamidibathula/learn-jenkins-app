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
                    echo 'Cleaning workspace...'
                    # Don't delete package-lock.json; only remove node_modules if needed
                    rm -rf node_modules

                    echo 'Setting npm cache directory...'
                    export NPM_CONFIG_CACHE=$(pwd)/.npm-cache

                    echo 'Building..'
                    echo 'Running on node:'
                    node --version
                    npm --version

                    echo 'Installing dependencies...'
                    npm ci  # or npm install if you must, but ci is better for CI

                    echo 'Running build...'
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
                echo 'Testing..'
                sh '''
                    test -f build/index.html
                    npm test
                '''
            }
        }
        stage('E2E Test') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.40.0-focal'
                    reuseNode true
                }
            }
            steps {
                echo 'Testing..'
                sh '''
                    npm install serve
                    node_modules/.bin/serve -s build &
                    sleep 10
                    np playwright test
                '''
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
    post {
        always {
                echo 'This will always run'
                junit 'jest-results/junit.xml'
        }
        success {
            echo 'This will run only if the build is successful'
        }
        failure {
            echo 'This will run only if the build fails'
        }
    }
}