pipeline {
  agent none
  environment  {
  MAJOR_VERSION = 1
  }
  stages {
    stage ('Say Hello') {
    agent any
      steps {
       sayHello 'Awesome Student'
      }
    }
   stage ('Say Hello') {
    agent any
      steps {
        echo "My Branch name: ${env.BRANCH_NAME}"
        
        script {
        def myLib = new linuxacademy.git.gitStuff();
          echo "My Commit: ${myLib.gitCommit("${env.WORKSPACE}/.git)}"
        }
      }
    }
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
        sh "if ![ -d '/var/www/html/rectangles/all/${env.BRANCH_NAME}' ]; then mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}; fi"
        sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
        }
    }
    stage("Running on CentoS") {
      agent {
       label 'CentOS'
      }
      steps {
        sh "wget http://neotech1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
        sh ""
      }
    }
    stage("Test on Debian") {
      agent {
       docker 'openjdk:8u171-jre-alpine'
      }
      steps {
       sh "wget -nc http://neotech1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar" 
       sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
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
       sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
      }
    }
    stage ('Promote development branch to master') {
      agent {
       label 'master'
      }
      when {
       branch 'development'
      }
      steps {
        echo "Staching Any Local Changes"
        sh 'git stash'
        echo "Checking out development branch"
        sh 'git checkout development'
        echo "Checking out master branch"
        sh 'git pull origin'
        sh 'git checkout master'
        echo "Merging development branch in master"
        sh 'git merge development'
        echo "Pushing to origin master"
        sh 'git push origin master'
        echo 'Tagging the release'
        sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
        sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
      }
     post {
      success {
      emailext(
        subject: "${env.JOB_NAME} [{$env.BUILD_NUMBER} Development Promoted to Master!]",
        body: """<p>'${env.JOB_NAME} [${env.BUILD_NUMBER}]' Development Promoted to Master!":</p>
        <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
        to: "mattlovegreen@gmail.com"
      )
      }
     }
    }
  }
    post {
      failure {
      emailext(
        subject: "${env.JOB_NAME} [{$env.BUILD_NUMBER} Failed!]",
        body: """<p>'${env.JOB_NAME} [${env.BUILD_NUMBER}]' Failed!":</p>
        <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
        to: "mattlovegreen@gmail.com"
      )
      }
    }
  }
