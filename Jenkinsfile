def previousBuildNumber = currentBuild.getPreviousBuild()?.getNumber()
pipeline {
  environment {
    dockerimagename = "narayanacharan/nginx-app:${BUILD_NUMBER}"
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
   
    stage('Deploy Blue') {
      when {
                branch 'master'
                changeset "refs/heads/master"
      }
      steps {
        withCredentials([string(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    writeFile(file: 'kubeconfig', text: KUBECONFIG)
                    sh "kubectl --kubeconfig=kubeconfig apply -f blue-deployment.yml"                 
        }
      }
    }

    stage('Deploy Green') {
      steps {
        withCredentials([string(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    writeFile(file: 'kubeconfig', text: KUBECONFIG)
                    sh "kubectl --kubeconfig=kubeconfig apply -f green-deployment.yml"                 
        }
      }
    }

    stage('Deploy Canary') {
      steps {
        withCredentials([string(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    writeFile(file: 'kubeconfig', text: KUBECONFIG)
                    sh "kubectl --kubeconfig=kubeconfig apply -f canary-deployment.yml"                 
        }
      }
    }
  }
}
