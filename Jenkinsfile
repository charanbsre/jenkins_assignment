pipeline {
  environment {
    dockerimagename = "narayanacharan/react-app-00"
    dockerImage = ""
  }
  agent any
  stages {
    stage('Checkout Source') {
      agent {
                docker {
                    image 'narayanacharan/jenkins-slave:7.0'
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
   
    stage('Deploy to Kubernetes') {
      steps {
        withCredentials([file(credentialsId: 'kubeconfig-secret', variable: 'KUBECONFIG')]) {
                    sh "kubectl --kubeconfig=${KUBECONFIG} apply -f hellowhale.yml"
        }
      }
    }
  }
}
