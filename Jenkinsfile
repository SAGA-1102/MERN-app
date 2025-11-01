pipeline {
  agent any

  environment {
    AWS_ACCOUNT_ID = '975050024946'
    AWS_REGION = 'ca-central-1'
  }

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/SAGA-1102/MERN-app.git', branch: 'main'
      }
    }

    stage('Build Docker Images') {
      steps {
        sh 'docker build -t frontend-saga ./client'
        sh 'docker build -t helloservice-saga ./server/helloService'
        sh 'docker build -t profileservice-saga ./server/profileService'
      }
    }

    stage('Push to ECR') {
      steps {
        sh '''
          aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com

          docker tag frontend-saga:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/frontend-saga:latest
          docker tag helloservice-saga:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/helloservice-saga:latest
          docker tag profileservice-saga:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/profileservice-saga:latest

          docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/frontend-saga:latest
          docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/helloservice-saga:latest
          docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/profileservice-saga:latest
        '''
      }
    }
  }
}
