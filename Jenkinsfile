pipeline {
    environment {
    dockerImage = ''
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git([url: 'https://github.com/saishivarcr/docker-demo.git', branch: 'main'])

      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "sai/docker-demo:${BUILD_NUMBER}"
        }
      }
    }
  }
}