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
        withCredentials([azureKeyVaultSecret(credentialsId: 'kubeconfig-secret', keyVaultUri: 'https://nextopsakv01.vault.azure.net', secretName: 'kubeconfig-secret', version: '1', variable: 'KUBECONFIG')]) {
                    sh "export KUBECONFIG=${KUBECONFIG}"
                    sh "kubectl --kubeconfig=${KUBECONFIG} apply -f hellowhale.yml"
        }
      }
    }
  }
}
