pipeline {
  agent {
  label 'master'
  }
  stages {
    stage ('Unit test') {
      steps{
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }
    stage ('build') {
      steps{
        sh 'ant -f build.xml -v'
      }
    }
    stage ('deply') {
      steps {
        sh 'cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/'
      }
    }
  }
  post {
    always {
      archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
    }
  }
}
