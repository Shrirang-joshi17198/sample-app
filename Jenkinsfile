pipeline {
    agent {
        docker {
            image 'python:3.9-alpine'
            args '-v /var/run/docker.sock:/var/run/docker.sock'  // For Docker-in-Docker builds
        }
    }

    environment {
        HOME = '.'
    }

    stages {
        stage('Checkout') {
            steps {
                // It's generally a good practice to clean workspace before checking out code
                cleanWs()
                checkout([$class: 'GitSCM', branches: [[name: 'main']], userRemoteConfigs: [[url: 'https://github.com/Shrirang-joshi17198/sample-app.git']]])
            }
        }

        stage('Build') {
            steps {
                sh 'pip install --user -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                sh 'pytest app/tests/'
            }
            post {
                always {
                    junit 'app/tests/junit_report.xml'  // Assuming JUnit-style test reports
                }
            }
        }

        stage('Deploy') {
            steps {
                // Replace with your deployment steps, e.g., using Docker plugins
                sh 'docker build -t my-python-app .'
                sh 'docker run -p 5000:5000 my-python-app'
            }
            post {
                failure {
                    sh 'docker rm -f my-python-app'  // Rollback logic for Docker containers
                }
            }
        }
    }
}
