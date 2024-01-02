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

    stage('Archive') {
      steps {
        archiveArtifacts artifacts: '.next/**/*', fingerprint: true
      }
    }

    stage('Copy') {
        agent {
            node {
                label 'master'
            }
        }
        steps {
            copyArtifacts filter: '.next/**/*', fingerprintArtifacts: true, projectName: 'demo-pipeline-archive', selector: lastSuccessful()
        }
    }
  }
}