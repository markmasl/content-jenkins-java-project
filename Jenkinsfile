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
        sh "mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}"
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
      }
    }
    stage("Running on CentoS") {
      agent {
      label 'CentOS'
      }
      steps {
        sh "wget http://neotech1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage("Test on Debian") {
      agent {
      docker 'openjdk:8u171-jre-alpine'
      }
      steps {
       sh "wget http://neotech1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar" 
       sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage ('Promote to green') {
      agent {
      label 'apache'
      }
      when {
      branch 'master'
      }
      steps {
       sh "cp /var/www/html/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
      }
    }
    stage ('Promote development branch to master') {
      agent {
      label 'master'
      }
      when {
      branch 'master'
      }
      steps {
        echo "Staching Any Local Changes"
        sh 'git slash'
        echo "Checking out development branch"
        sh 'git checkout development'
        echo "Checking out master branch"
        sh 'git checkout master'
        echo "Merging development branch in master"
        sh 'git merge development'
        echo "Pushing to origin master"
        sh 'git push origin master'
      }
    }
  }
}
