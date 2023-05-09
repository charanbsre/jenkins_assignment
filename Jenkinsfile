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
        script {
            sh "hostname"
            sh "pwd"
            sh "ls -ltr /usr/bin"
            sh "ls -ltr /root/.kube"
            sh "which kubectl"            
            sh "kubectl config get-contexts"
            sh "kubectl config set-context NextOpsAKS01"
            sh "kubectl apply -f bluewhale.yml"
        }
      }
    }
  }
}
