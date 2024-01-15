pipeline {
    agent any
    
    stages {
        stage('Install Python') {
            steps {
                script {
                    sh 'apt-get update && apt-get install -y python3'
                }
            }
        }

        stage('Automated Testing in CI') {
            steps {
                script {
                    // Checkout code from version control
                    checkout scm
                    
                    // Install dependencies
                    sh 'python --version'
                    sh 'pip install --no-cache-dir -r requirements.txt'
                    
                    // Run automated tests
                    sh 'pip install pytest'
                    sh 'pytest'
                }
            }
        }
        
        stage('Rollback Mechanism in CD') {
            steps {
                script {
                    // Deploy the application
                    docker.build("my-flask-app")
                    docker.withRegistry('https://hub.docker.com/', 'dockerhub') {
                        dockerImage.push()
                    }
                    
                    // Introduce a deliberate fault
                    // ...

                    // Detect deployment failure and initiate rollback
                    // ...

                    // Confirm the rollback
                    // ...
                }
            }
        }
    }
    
    post {
        always {
            // Clean up
            script {
                sh 'docker rmi my-flask-app' // Remove Docker image
            }
        }
    }
}
