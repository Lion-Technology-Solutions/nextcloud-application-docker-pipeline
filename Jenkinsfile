pipeline {

    tools{

        maven 'maven3.9.2'
    }
    agent any

    environment {
        registry = "768477844960.dkr.ecr.ca-central-1.amazonaws.com/nextcloud"
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
                    sh 'aws ecr get-login-password --region ca-central-1 | docker login --username AWS --password-stdin 768477844960.dkr.ecr.ca-central-1.amazonaws.com'
                    sh 'docker push 768477844960.dkr.ecr.ca-central-1.amazonaws.com/nextcloud:$BUILD_NUMBER'
                    
                }
            }
        } 

        stage("list images") {
            steps{
                sh "docker images"
                
            }
        }
        // stage('notify teams channel'){
        //     steps{

        //        office365ConnectorSend message: 'Congratulations class20', webhookUrl: 'https://liontechacademy.webhook.office.com/webhookb2/1c45852e-99e7-4aa7-a7f5-be60526bb887@b290c85e-0692-4ada-8f25-2a3b60aca73e/JenkinsCI/38f55bedaf4142bc8366233b562335b3/90dc930a-1d3a-4c50-9c2d-39d010312f55' 
        //     }
        // }
   
    }
}