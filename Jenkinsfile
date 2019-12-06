pipeline {
  agent any
  tools { 
        maven 'Maven'
        jdk 'JAVA'
  }
  stages {
    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace... */
      steps {
        checkout scm
      }
    }
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
        sh 'echo $USER'
        sh 'echo whoami'
      }
    }
    stage('Docker Build') {
      steps {
        sh '/usr/bin/docker build -t address-service .'
      }
    }
   
    stage('push image to ECR'){
      steps {
       withDockerRegistry(credentialsId: 'ecr:us-east-1:aws-credentials', url: 'http://650408071817.dkr.ecr.us-east-1.amazonaws.com/address-service:latest') {
          sh 'docker tag address-service:latest 650408071817.dkr.ecr.us-east-1.amazonaws.com/address-service:latest'
          sh 'docker push 650408071817.dkr.ecr.us-east-1.amazonaws.com/address-service:latest'
        } 
      }
    }
  stage('deploy to ECR') {
      steps {
        node('eks-master-node'){
         checkout scm
         sh 'kubectl apply -f deployment.yaml' 
         sh 'kubectl apply -f service.yaml' 
        }
      }
    } 
  }
}
