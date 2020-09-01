pipeline {
  agent any
  tools {nodejs 'node' }
  stages {
    stage('Cloning Git') {
      steps {
        checkout scm
      }
    }
    stage('Build') {
       steps {
         sh 'npm install'
       }
    }
    stage('Test') {
      steps {
        sh 'npm test'
      }
    }
    stage('Docker Build') {
      steps {
        sh 'sudo docker build -t nodejs .'
        sh 'echo build finished'
      }
    }
    stage('push image to ECR'){
      steps {
        sh 'echo entered ecr'
       withDockerRegistry(credentialsId: 'ecr:us-east-1:aws-cred', url: '102789521217.dkr.ecr.us-east-1.amazonaws.com/nodejs') {
          sh 'sudo usermod -aG docker jenkins'
          sh 'docker tag nodejs:latest 102789521217.dkr.ecr.us-east-1.amazonaws.com/nodejs:latest'
          sh 'docker push 102789521217.dkr.ecr.us-east-1.amazonaws.com/nodejs:latest'
        } 
      }
    }
      stage('deploy to EKS CLUSTER') {
      steps {
         node('eks-master-node'){
            checkout scm
         sh 'aws eks --region us-east-1 update-kubeconfig --name terraform-eks-demo'
         sh 'kubectl apply -f deployment.yaml' 
         sh 'kubectl apply -f service.yaml'  
         }
      }
    } 
  }
}
