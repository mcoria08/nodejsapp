pipeline {
  agent any
  environment {
    registry = "896304481314.dkr.ecr.us-east-2.amazonaws.com/jenkins-pipeline-build-demo-iii"
  }
  stages {
    stage('Cloning Git') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/mcoria08/nodejsapp']]])
      }
    }
    
    stage('Docker build'){
        steps{
          script {
            dockerImage = docker.build registry
          }
        }
    }
    
    stage('Docker push'){
        steps{
          script {
            sh 'aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 896304481314.dkr.ecr.us-east-2.amazonaws.com'
            sh 'docker push 896304481314.dkr.ecr.us-east-2.amazonaws.com/jenkins-pipeline-build-demo-iii:latest'
          }
        }
    }
    
    // Stopping Docker containers for cleaner Docker run
    stage('stop previous containers') {
      steps {
        sh 'docker ps -f name=nodejsapp -q | xargs --no-run-if-empty docker container stop'
        sh 'docker container ls -a -fname=nodejsapp -q | xargs -r docker container rm'
      }
    }
   
    stage('Docker Run') {
      steps{
        script {
          sh 'docker run -d -p 8096:5000 --rm --name py 896304481314.dkr.ecr.us-east-2.amazonaws.com/jenkins-pipeline-build-demo-iii:latest'
        }
      }
    }
  }
}