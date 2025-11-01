pipeline {
  agent any

  environment {
    AWS_ACCOUNT_ID = '975050024946'
    AWS_REGION = 'ca-central-1'
    ECR_URL = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
    DOCKER_BUILDKIT = '0' // 👈 disables Buildx to avoid exec format error
  }

  stages {
    stage('Checkout from GitHub') {
      steps {
        git url: 'https://github.com/SAGA-1102/MERN-app.git', branch: 'main'
      }
    }

    stage('Build Docker Images') {
      steps {
        sh 'docker build -t frontend-saga ./frontend/'
        sh 'docker build -t helloservice-saga ./backend/helloService'
        sh 'docker build -t profileservice-saga ./backend/profileService'
      }
    }

    stage('Debug AWS CLI') {
  steps {
    sh 'aws --version'
    sh 'aws sts get-caller-identity || echo "Failed to get identity"'
  }
}

    stage('Authenticate to ECR') {
      steps {
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: 'aws-creds'
        ]]) {
          sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_URL}"
        }
      }
    }

    stage('Tag and Push Images') {
      steps {
        sh "docker tag frontend-saga:latest ${ECR_URL}/frontend-saga:latest"
        sh "docker tag helloservice-saga:latest ${ECR_URL}/helloservice-saga:latest"
        sh "docker tag profileservice-saga:latest ${ECR_URL}/profileservice-saga:latest"

        sh "docker push ${ECR_URL}/frontend-saga:latest"
        sh "docker push ${ECR_URL}/helloservice-saga:latest"
        sh "docker push ${ECR_URL}/profileservice-saga:latest"
      }
    }
  }
}
