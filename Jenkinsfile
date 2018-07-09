pipeline {
  agent none
  
  stages {
    stage ('Unit test') {
      agent {
        label 'apache'
        }
      steps{
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }
    stage ('build') {
      agent {
        label 'apache'
      }
      steps{
        sh 'ant -f build.xml -v'
      }
      post {
         success {
           archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
    }
  }
    }
    stage ('deploy') {
      agent {
        label 'apache'
      }
      steps {
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
      }
    }
    stage("Running on CentoS") {
      agent {
      label 'CentOS'
      }
      steps {
      sh "wget http://neotech1.mylabserver.com/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
      sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage("Test on Debian") {
      agent {
      docker 'openjdk:8u171-jre-alpine'
      }
      steps {
      sh "whet http://neotech1.mylabserver.com/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar" 
      sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
  }
}
