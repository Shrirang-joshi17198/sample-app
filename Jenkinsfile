pipeline {
  agent {
    docker {
      image 'python:3.9-alpine'
      args '-v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/jenkins/workspace/task@5:/var/lib/jenkins/workspace/task@5:rw,z'
    }
  }

  environment {
    HOME = '.'
  }

  stages {
    stage('Checkout') {
      steps {
        cleanWs()
        checkout([$class: 'GitSCM', branches: [[name: 'main']], userRemoteConfigs: [[url: 'https://github.com/Shrirang-joshi17198/sample-app.git']]])
      }
    }

    stage('Build') {
      steps {
        sh 'cd /var/lib/jenkins/workspace/task@5 && pip install --no-cache-dir -r requirements.txt'
        sh 'pip install pytest markupsafe==2.0.1'
      }
    }

    stage('Test') {
      steps {
        sh 'pytest app/tests/'
      }
      post {
        always {
          junit 'app/tests/junit_report.xml'
        }
      }
    }

    stage('Build Image') {
      steps {
        sh 'docker build -t my-flask-app .'
      }
    }

    stage('Deploy') {
      steps {
        sh 'docker run -d -p 5000:5000 my-flask-app'
      }
    }

    stage('Rollback') {
      when {
        expression { currentBuild.resultIsBetterOrEqualTo('FAILURE') }
      }
      steps {
        sh 'docker rm -f $(docker ps -q -f name=my-flask-app)'  // Stop and remove the running container
        // Additional rollback logic if needed, e.g., redeploying a previous version
      }
    }
  }
}
