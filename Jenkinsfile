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
    artifactPaths = '.next/**,package.json,package-lock.json'
    deployDir = '/root/app/demo'
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
          archiveArtifacts artifacts: "${artifactPaths}", onlyIfSuccessful: true
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
              projectName: env.JOB_NAME,
              selector: specific(env.BUILD_NUMBER),
              target: env.deployDir
            )
            sh "cd ${env.deployDir}"
            sh 'bun install --production'
            sh 'pm2 delete DemoARAIC || true'
            sh 'pm2 --name DemoARAIC start npm -- start && pm2 save -f'
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
              projectName: env.JOB_NAME,
              selector: specific(env.BUILD_NUMBER),
              target: env.deployDir
            )
            sh "cd ${env.deployDir}"
            sh 'bun install --production'
            sh 'pm2 delete DemoARAIC || true'
            sh 'pm2 --name DemoARAIC start npm -- start && pm2 save -f'
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
              projectName: env.JOB_NAME,
              selector: specific(env.BUILD_NUMBER),
              target: env.deployDir
            )
            sh "cd ${env.deployDir}"
            sh 'bun install --production'
            sh 'pm2 delete DemoARAIC || true'
            sh 'pm2 --name DemoARAIC start npm -- start && pm2 save -f'
          }
        }
      }
    }
  }
}
