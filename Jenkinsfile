pipeline {
  environment {
    dockerimagename = "narayanacharan/react-app-00"
    dockerImage = ""
    SECRET_KEY = credentials('secret-key')
  }
  agent none
  stages {
    stage('Checkout Source') {
      agent {
                docker {
                    image 'narayanacharan/jenkins-slave:1.0'
                    label 'java-docker-slave'
                }
      }
      steps {
          git credentialsId: 'github', url: 'https://github.com//charanbsre/jenkins_assignment.git'
      }
    }
    
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }
    
    stage('Pushing Image') {
      environment {
               registryCredential = 'dhub'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }
    stage('Foo') {
      steps {
        echo SECRET_KEY
        echo SECRET_KEY.substring(0, SECRET_KEY.size() -1) // shows the right secret was loaded, don't do this for real secrets unless you're debugging 
      }
    }
  }
}
