pipeline {
    agent {
        docker {
            image 'python:3.9-alpine'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        HOME = '.'
    }

    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                script {
                    checkout([$class: 'GitSCM', branches: [[name: 'main']], userRemoteConfigs: [[url: 'https://github.com/Shrirang-joshi17198/sample-app.git']]])
                }
            }
        }

        stage('Build and Test') {
            agent any
            steps {
                script {
                    docker.image('python:3.9-alpine').inside('-v $PWD:/flask_app') {
                        sh 'pip install --no-cache-dir -r /flask_app/requirements.txt'
                        sh 'pip install pytest'
                        sh 'pip install markupsafe==2.0.1'
                        sh 'pytest /flask_app/app/tests/'
                    }
                }
            }
            post {
                always {
                    junit '/flask_app/app/tests/junit_report.xml'
                }
            }
        }

        stage('Deploy') {
            agent any
            steps {
                script {
                    try {
                        docker.image('python:3.9-alpine').inside('-v $PWD:/flask_app') {
                            sh 'docker build -t my-flask-app /flask_app'
                            sh 'docker run -p 5000:5000 my-flask-app'
                        }
                    } catch (Exception e) {
                        echo "Deployment failed. Rolling back..."
                        rollback()
                        error "Deployment failed."
                    }
                }
            }
            post {
                failure {
                    sh 'docker rm -f my-flask-app'
                }
            }
        }

        stage('Rollback') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('FAILURE') }
            }
            steps {
                script {
                    echo "Rolling back..."
                    rollback()
                }
            }
        }
    }
}

def rollback() {
    // Implement your rollback logic here
    sh 'echo "Rollback logic goes here"'
    // Example: sh 'docker stop my-flask-app-previous-container'
}
