pipeline {
  agent any

  environment {
    AWS_ACCOUNT_ID = '975050024946'
    AWS_REGION = 'ca-central-1'
    ECR_URL = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
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

    stage('Authenticate to ECR') {
      steps {
        sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_URL}"
      }
    }

    stage('Tag Docker Images') {
      steps {
        sh "docker tag frontend-saga:latest ${ECR_URL}/frontend-saga:latest"
        sh "docker tag helloservice-saga:latest ${ECR_URL}/helloservice-saga:latest"
        sh "docker tag profileservice-saga:latest ${ECR_URL}/profileservice-saga:latest"
      }
    }

    stage('Push to ECR') {
      steps {
        sh "docker push ${ECR_URL}/frontend-saga:latest"
        sh "docker push ${ECR_URL}/helloservice-saga:latest"
        sh "docker push ${ECR_URL}/profileservice-saga:latest"
      }
    }
  }
}
