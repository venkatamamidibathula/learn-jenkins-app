pipeline {
    agent any

    stages {
        stage('Prepare') {
            steps {
                // Ensure workspace permissions are correct
                sh '''
                    echo "Preparing workspace..."
                    ls -la
                    chmod -R u+w .
                '''
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-u $(id -u):$(id -g)' // Run as the same user as the host
                }
            }
            steps {
                sh '''
                    echo "Building the application..."
                    
                    # Verify Node.js and npm versions
                    node --version
                    npm --version
                    
                    # Clear npm cache to prevent corruption
                    npm cache clean --force
                    
                    # Install dependencies using npm ci
                    echo "Installing dependencies..."
                    npm ci
                    
                    # Build the application
                    echo "Running build script..."
                    npm run build
                    
                    # List directory contents after build
                    ls -la
                '''
            }
        }
    }

    post {
        always {
            // Clean up after the build
            sh '''
                echo "Cleaning up workspace..."
                rm -rf node_modules
            '''
        }
        success {
            echo "Build completed successfully!"
        }
        failure {
            echo "Build failed. Check logs for details."
        }
    }
}