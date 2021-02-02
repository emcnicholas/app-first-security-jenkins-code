pipeline {
  environment {
    registry = "/my-cicd-app"
    registryCredential = 'dockerhub'
    dockerImage = ''
  }
  agent {
    dockerfile true
  }
  stages {
    stage('Test App') {
      steps {
        sh 'python3 test.py'
      }
      post {
        always {
          junit 'test-reports/*.xml'
        }
      }
    }
    // Uncomment for SAST lab step
    // Commented section starts

    stage('SAS Test') {
      steps {
        snykSecurity(
          snykInstallation: 'SnykV2Plugin',
          snykTokenId: 'snyktoken',
          severity: 'medium',
          failOnIssues: true)
      }
    }

    // Commented section ends
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Upload Image to Registry') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
  }
}