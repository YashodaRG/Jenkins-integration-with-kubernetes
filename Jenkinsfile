pipeline {
    agent any
    environment {
        registry = "241916522557.dkr.ecr.ap-south-1.amazonaws.com/node-app"
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], userRemoteConfigs: [[url: 'https://github.com/YashodaRG/java-example.git']]])     
            }
        }
  
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
	stage('Pushing to ECR') {
     steps{ 
	    script {
		withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'github']]){
           sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 241916522557.dkr.ecr.ap-south-1.amazonaws.com'
           sh 'docker push 241916522557.dkr.ecr.ap-south-1.amazonaws.com/node-app:latest'
         }
        }
      }
	 }
	
                 
    stage('Deploy to k8s'){
            steps{
                script{
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'Kubeconfpwd')
            }
      }
    }
	    
}
    }
