pipeline {
   tools {
        maven 'Maven3'
    }
    agent any
    environment {
        registry = "004738182300.dkr.ecr.us-east-2.amazonaws.com/claimvisionecr"
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/ponnamnagesh/myKubernetesDeployjob']]])     
            }
        }
      stage ('Build') {
          steps {
            sh 'mvn clean install'           
            }
      }
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          //dockerImage = docker.build registry 
          sh 'docker build -t claimvisionecr .'
          sh 'docker tag claimvisionecr:latest 004738182300.dkr.ecr.us-east-2.amazonaws.com/claimvisionecr:latest'
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh 'aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 004738182300.dkr.ecr.us-east-2.amazonaws.com'
                sh 'docker push 004738182300.dkr.ecr.us-east-2.amazonaws.com/claimvisionecr:latest'
         }
        }
      }

       stage('K8S Deploy') {
        steps{   
            script {
                withKubeConfig([credentialsId: 'k8s', serverUrl: '']) {
                sh ('kubectl apply -f  eks-deploy-k8s.yaml')
                }
            }
        }
       }
    }
}
