pipeline{
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
                    rm -rf node_modules package-lock.json
                    echo 'Setting npm cache directory...'
                    export NPM_CONFIG_CACHE=$(pwd)/.npm-cache
                    echo 'Building..'
                    echo 'Running on node:'
                    node --version
                    npm --version
                    echo 'Installing dependencies...'
                    npm install
                    npm run build
                    ls -la
                '''
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
                sh '''
                    sh 'test -f build/index.html'

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
        }
        success {
            echo 'This will run only if the build is successful'
        }
        failure {
            echo 'This will run only if the build fails'
        }
    }
}