pipeline {

    tools{

        maven 'maven3.9.2'
    }
    agent any

    environment {
        AWS_ACCOUNT_ID = '123456789012'
        AWS_REGION = 'ca-central-1'
        ECR_REPOSITORY = 'myapps'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        CLUSTER_NAME = 'class30'
        KUBE_CONFIG = credentials('eks-kubeconfig')
        
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Lion-Technology-Solutions/nextcloud-application-docker-pipeline.git']])
            }
        }
        
        stage ("Build Image") {
            steps {
                script {
                    dockerImage = docker.build registry
                    dockerImage.tag("$BUILD_NUMBER")
                }
            }
        }
        
        stage ("Push to ECR") {
            steps {
                script {
                    sh 'aws ecr get-login-password --region ca-central-1 | docker login --username AWS --password-stdin 757750585556.dkr.ecr.ca-central-1.amazonaws.com'
                    sh 'docker push  757750585556.dkr.ecr.ca-central-1.amazonaws.com/myapps:$BUILD_NUMBER'
                    
                }
            }
        } 

        stage('Configure kubectl') {
            steps {
                sh """
                mkdir -p ~/.kube
                echo "${KUBE_CONFIG}" > ~/.kube/config
                aws eks update-kubeconfig --name ${CLUSTER_NAME} --region ${AWS_REGION}
                """
            }
        } 

        stage('Deploy to EKS') {
            steps {
                sh """
                # Update image tag in deployment.yaml
                sed -i 's|IMAGE_TAG|${IMAGE_TAG}|g' k8s/deployment.yaml
                
                # Apply Kubernetes manifests
                kubectl apply -f k8s/namespace.yaml
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                
                # Verify deployment
                kubectl rollout status deployment/my-app -n my-namespace
                """
            }
        }

    }
}