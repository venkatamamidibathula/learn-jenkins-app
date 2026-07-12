pipeline {
    agent any

    environment {
        // Point npm cache into the workspace, not /.npm
        NPM_CONFIG_CACHE = "${WORKSPACE}/.npm"
        NETLIFY_SiTE_ID = "b9398f62-1107-4461-adb2-0b8affb40254"
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
                    echo 'Cleaning workspace...'
                    rm -rf node_modules

                    echo 'Setting npm cache directory...'
                    export NPM_CONFIG_CACHE=$(pwd)/.npm-cache

                    echo 'Building..'
                    node --version
                    npm --version

                    echo 'Installing dependencies...'
                    npm ci

                    echo 'Running build...'
                    npm run build

                    ls -la
                '''
            }
        }
        stage('Tests'){
            parallel {
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
                                image 'mcr.microsoft.com/playwright:v1.39.0-focal'
                                reuseNode true
                            }
                        }
                        steps {
                            echo 'Testing..'
                            sh '''
                                # Ensure npm uses workspace cache
                                export NPM_CONFIG_CACHE=$(pwd)/.npm-cache

                                npm install serve
                                node_modules/.bin/serve -s build &
                                sleep 10
                                npx playwright test --reporter=html
                            '''
                        }
                    }

            }
        }

        stage('Archive') {
            steps {
                echo 'Archiving build artifacts...'
                archiveArtifacts artifacts: 'build/**', fingerprint: true
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-u root:root'
                }
            }
            steps {
                sh '''
                    echo 'Deploying...'
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify --version
                    echo "Deploying to Netlify.... Site: $NETLIFY_SITE_ID"

                '''
            }
        }
    stage('Clean') {
    agent {
        docker {
            image 'node:18-alpine'
            reuseNode true
            args '-u root:root'
        }
    }
    steps {
        sh 'rm -rf node_modules build .npm'
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