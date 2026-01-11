pipeline {
  agent any

  environment {
    AWS_ACCOUNT_ID = '768477844960'
    AWS_REGION     = 'ca-central-1'
    ECR_REPOSITORY = 'nextcloud'
    IMAGE_TAG      = "${env.BUILD_NUMBER}"
    ECR_REGISTRY   = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
    IMAGE_URI      = "${ECR_REGISTRY}/${ECR_REPOSITORY}:${IMAGE_TAG}"

    // If your ~/.aws/credentials uses a non-default profile, uncomment and set it:
     AWS_PROFILE = 'default'
  }

  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/Lion-Technology-Solutions/nextcloud-application-docker-pipeline.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh '''
          set -euo pipefail
          docker version
          docker build -t "$IMAGE_URI" .
        '''
      }
    }

    stage('Login to ECR') {
      steps {
        sh '''
          set -euo pipefail
          aws --version
          aws sts get-caller-identity

          aws ecr get-login-password --region "$AWS_REGION" \
            | docker login --username AWS --password-stdin "$ECR_REGISTRY"
        '''
      }
    }

    stage('Push to ECR') {
      steps {
        sh '''
          set -euo pipefail
          docker push "$IMAGE_URI"
        '''
      }
    }
  }
}
