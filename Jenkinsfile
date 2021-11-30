pipeline {
  environment {
        imagename = 'sai/demo-docker'
        dockerImage = ''
  }
  agent any
  options {
        ansiColor('xterm')
    }
  stages {
    stage('Building Docker Image') {
      steps{
        script {
          dockerImage = docker.build "${imagename}:${BUILD_NUMBER}"
        }
      }
    }
    stage('Docker Image Scan With Dockle') {
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
                                
                sh "./dockle --exit-code 0 ${imagename}:${BUILD_NUMBER} | tee dockle-${BUILD_NUMBER}.out"

                sh "cat dockle-${BUILD_NUMBER}.out | aha > dockle-${BUILD_NUMBER}.html"

                sh "wkhtmltopdf dockle-${BUILD_NUMBER}.html dockle-${BUILD_NUMBER}.pdf"

                archiveArtifacts artifacts: '*.pdf', fingerprint: true
            }
        }
    stage('Docker Image Scan With Trivy') {
            when {
              expression {
                currentBuild.result == null || currentBuild.result == 'SUCCESS' 
              }
            }
            steps {
                sh '''VERSION=$(curl --silent "https://api.github.com/repos/aquasecurity/trivy/releases/latest" | grep \'"tag_name":\' | sed -E \'s/.*"v([^"]+)".*/\\1/\') && \\
                wget https://github.com/aquasecurity/trivy/releases/download/v${VERSION}/trivy_${VERSION}_Linux-64bit.tar.gz && \\
                tar zxvf trivy_${VERSION}_Linux-64bit.tar.gz'''
                                

                sh "./trivy --no-progress ${imagename}:${BUILD_NUMBER} | tee trivy-${BUILD_NUMBER}.out"

                sh "cat trivy-${BUILD_NUMBER}.out | aha > trivy-${BUILD_NUMBER}.html"

                sh "wkhtmltopdf trivy-${BUILD_NUMBER}.html trivy-${BUILD_NUMBER}.pdf"

                archiveArtifacts artifacts: '*.pdf', fingerprint: true
            }
        }
  }
}