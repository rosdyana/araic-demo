pipeline {
  agent {
    node {
      label 'master'
    }
  }

  options {
    skipDefaultCheckout(true)
  }

  environment {
    currentJobName = "${env.JOB_NAME}"
    currentBuildNumber = "${env.BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps {
        echo 'checking out source code...'
        checkout scm
      }
    }

    stage('Install Dependencies') {
      steps {
        echo 'Installing dependencies...'
        sh 'bun install --production'
      }
    }

    stage('Build') {
      steps {
        echo 'Building...'
        sh 'bun run build'
      }
    }

    stage('Archive the artifacts') {
      steps {
        script {
          echo 'Archiving the artifacts...'
          def artifactPaths = '.next/**,package.json,package-lock.json'
          archiveArtifacts artifacts: artifactPaths, onlyIfSuccessful: true
        }
      }
    }

    stage('Deployment') {
      parallel {
        stage('Deploy to Staging') {
          when {
            expression {
              env.BRANCH_NAME != 'main'
            }
          }
          agent { label 'taichung' }

          steps {
            echo 'Deploying to staging...'
            copyArtifacts(
              filter: '**',
              projectName: currentJobName,
              selector: specific(
                currentBuildNumber
              ),
              target: '/root/app/demo'
            )
            sh 'cd /root/app/demo'
            sh 'pm2 delete DemoARAIC || true'
            sh 'pm2 --name DemoARAIC start bun -- start && pm2 save -f'
          }
        }

        stage('Deploy to Production - 1') {
          when {
            expression {
              env.BRANCH_NAME == 'main'
            }
          }
          agent { label 'tainan' }
          steps {
            echo 'Deploying to production...'
            copyArtifacts(
              filter: '**',
              projectName: currentJobName,
              selector: specific(
                currentBuildNumber
              ),
              target: '/root/app/demo'
            )
            sh 'cd /root/app/demo'
            sh 'pm2 delete DemoARAIC || true'
            sh 'pm2 --name DemoARAIC start bun -- start && pm2 save -f'
          }
        }

        stage('Deploy to Production - 2') {
          when {
            expression {
              env.BRANCH_NAME == 'main'
            }
          }
          agent { label 'kaohsiung' }
          steps {
            echo 'Deploying to production...'
            copyArtifacts(
              filter: '**',
              projectName: currentJobName,
              selector: specific(
                currentBuildNumber
              ),
              target: '/root/app/demo'
            )
            sh 'cd /root/app/demo'
            sh 'pm2 delete DemoARAIC || true'
            sh 'pm2 --name DemoARAIC start bun -- start && pm2 save -f'
          }
        }
      }
    }
  }
}
