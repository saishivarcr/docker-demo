pipeline {
    environment {
    dockerImage = ''
  }
  agent any
  stages {
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "sai/docker-demo:${BUILD_NUMBER}"
        }
      }
    }
  }
}