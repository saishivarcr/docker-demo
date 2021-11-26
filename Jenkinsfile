pipeline {
    environment {
        imagename = 'sai/demo-docker'
        dockerImage = ''
  }
  agent any
  stages {
    stage('Building Docker Image') {
      steps{
        script {
          dockerImage = docker.build "${imagename}:${BUILD_NUMBER}"
        }
      }
    }
    stage('Docker Image Scan') {
            when {
              expression {
                currentBuild.result == null || currentBuild.result == 'SUCCESS' 
              }
            }
            steps {
                sh '''VERSION=$(
                      curl --silent "https://api.github.com/repos/goodwithtech/dockle/releases/latest" | \\
                      grep \'"tag_name":\' | \\
                      sed -E \'s/.*"v([^"]+)".*/\\1/\' \\
                      ) && curl -L -o dockle.tar.gz https://github.com/goodwithtech/dockle/releases/download/v${VERSION}/dockle_${VERSION}_Linux-64bit.tar.gz &&  \\
                      tar zxvf dockle.tar.gz'''
                sh "./dockle --exit-code 0 ${imagename}:${BUILD_NUMBER}"
            }
        }
  }
}