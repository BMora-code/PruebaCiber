pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t flask_vulnerable_app .'
      }
    }

    stage('Unit Tests') {
      steps {
        sh "echo 'No unit tests implemented'"
      }
    }

    stage('SAST - Bandit') {
      steps {
        sh """
        pip install bandit
        bandit -r . -f json -o bandit_report.json || true
        """
        archiveArtifacts artifacts: 'bandit_report.json'
      }
    }

    stage('DAST - OWASP ZAP') {
      steps {
        sh """
        docker run --network=host \
          -v $(pwd)/zap:/zap \
          owasp/zap2docker-stable zap-baseline.py \
          -t http://127.0.0.1:5000 \
          -r report.html || true
        """
      }
    }

    stage('Deploy') {
      steps {
        sh 'docker compose up -d --build'
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: '**/report.html'
    }
  }
}