pipeline {
  agent any
  options {
   buildDiscarder(logRotator(numToKeepStr: '2', artifactBNumToKeepStr: '1'))
  }
  stages {
    stage ('build') {
      steps{
        sh 'ant -f build.xml -v'
      }
    }
  }
  post {
    always {
      archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
    }
  }
}
