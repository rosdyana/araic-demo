pipeline {
  agent {
    node {
      label 'agent001'
    }

  }
  stages {
    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Build') {
      steps {
        sh 'npm run build'
      }
    }

    stage('Deploy with PM2') {
      steps {
        sh 'pm2 --name DemoARAIC start npm -- start && pm2 save -f'
      }
    }

  }
}