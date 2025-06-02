pipeline {
  agent any
  tools {
    nodejs 'NodeJS-20.x'
  }
  environment {
    CI = 'true'
    DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
  }
  stages {
    stage('Install') {
      steps {
        dir('app') {
          sh 'npm install'
        }
      }
    }
    stage('Build') {
      steps {
        dir('app') {
          sh 'npm run build'
        }
      }
    }
    stage('Test') {
      steps {
        dir('app') {
          sh 'npm test'
        }
      }
    }
    stage('Docker Build & Push') {
      steps {
        dir('app') {
          script {
            def image = "your-dockerhub-username/jenkins-node-app:${env.BUILD_NUMBER}"
            sh "docker build -t $image ."
            sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
            sh "docker push $image"
          }
        }
      }
    }
    stage('Deploy') {
      steps {
        dir('app') {
          script {
            if (env.BRANCH_NAME == 'main') {
              sh 'npm run deploy:prod'
            } else {
              sh 'npm run deploy:stage'
            }
          }
        }
      }
    }
  }
}
