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
        //CLUSTER_NAME = 'class30'
        //KUBE_CONFIG = credentials('eks-kubeconfig')
        
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Lion-Technology-Solutions/nextcloud-application-docker-pipeline.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}")
                }
            }
        }
        
    
        stage('Push to ECR') {
            steps {
                withAWS(credentials: 'aws-credentials', region: "${AWS_REGION}") {
                    script {
                        // Login to ECR
                        sh """
                        aws ecr get-login-password --region ${AWS_REGION} | \
                        docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                        """
                        
                        // Push the image
                        docker.withRegistry("https://${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com") {
                            docker.image("${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}").push()
                        }
                    }
                }
            }
        
}

        // stage('Configure kubectl') {
        //     steps {
        //         sh """
        //         mkdir -p ~/.kube
        //         echo "${KUBE_CONFIG}" > ~/.kube/config
        //         aws eks update-kubeconfig --name ${CLUSTER_NAME} --region ${AWS_REGION}
        //         """
        //     }
        // } 

        // stage('Deploy to EKS') {
        //     steps {
        //         sh """
        //         # Update image tag in deployment.yaml
        //         sed -i 's|IMAGE_TAG|${IMAGE_TAG}|g' k8s/deployment.yaml
                
        //         # Apply Kubernetes manifests
        //         kubectl apply -f k8s/namespace.yaml
        //         kubectl apply -f k8s/deployment.yaml
        //         kubectl apply -f k8s/service.yaml
                
        //         # Verify deployment
        //         kubectl rollout status deployment/my-app -n my-namespace
        //         """
        //     }
        // }

    //}
//}





