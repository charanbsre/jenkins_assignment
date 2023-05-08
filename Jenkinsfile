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
                    image 'narayanacharan/jenkins-slave:5.0'
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
    stage('Deploy to Kubernetes') {
      steps {
        script {
            sh "hostname"
            sh "pwd"
            sh 'export PATH=$PATH:/usr/bin/az'
            sh 'cd /usr/bin'
            sh "./az login --service-principal -u 1ee02188-99db-424b-bf71-11772c7da4c5 -p Mfj8Q~SEeIdynNyTCCuKrQdfAa15i5BdRCzTEaFV --tenant 9085ff8c-8807-4ff8-a403-ea470525cda6"
            sh "./az account set --subscription a355c32e-4a22-4b05-aab4-be236850fa6e"
            sh "./az aks get-credentials --resource-group NextOps --name NextOpsAKS01"
            sh "kubectl config get-contexts"
            sh "kubectl config set-context NextOpsAKS01"
            sh "kubectl apply -f bluewhale.yml"
        }
      }
    }
  }
}
