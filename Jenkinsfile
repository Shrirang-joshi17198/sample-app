pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Shrirang-joshi17198/sample-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("your-image-name:${env.BUILD_ID}")
                }
            }
        }

        stage('Run Unit Tests') {
            steps {
                script {
                    dockerImage.inside {
                        sh "pytest tests/"
                    }
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                script {
                    docker.withRegistry('https://hub.docker.com/', 'dockerhub') {
                        dockerImage.push()
                    }
                    // Replace with your specific deployment steps
                    // Option 1: Manual commands
                    sh "docker run -d -p 5000:5000 your-image-name:${env.BUILD_ID}"
                    // Option 2: Ansible (assuming Ansible is installed)
                    ansiblePlaybook credentialsId: 'ansible-credentials', installation: 'ansible', playbook: 'deploy.yml'
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace
            deleteDir()
        }

        failure {
            // Implement rollback tailored to your deployment mechanism
            // ... (rollback steps remain the same)
        }
    }
}
