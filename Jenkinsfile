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
      when {
                changeset "refs/heads/dev"
      }
      steps {
          git credentialsId: 'github', branch: 'dev', url: 'https://github.com//charanbsre/jenkins_assignment.git'
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
                changeset "refs/heads/master"
      }
      steps {
        withCredentials([string(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    writeFile(file: 'kubeconfig', text: KUBECONFIG)
                    sh "kubectl --kubeconfig=kubeconfig apply -f manifests/blue-deployment.yml"                 
        }
      }
    }

    stage('Deploy Green') {
      when {
                changeset "refs/heads/dev"
      }
      steps {
        withCredentials([string(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    writeFile(file: 'kubeconfig', text: KUBECONFIG)
                    sh "kubectl --kubeconfig=kubeconfig apply -f manifests/green-deployment.yml"                 
        }
      }
    }

    stage('Deploy Canary') {
      when {
                changeset "refs/heads/dev"
      }
      steps {
        withCredentials([string(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    writeFile(file: 'kubeconfig', text: KUBECONFIG)
                    sh "kubectl --kubeconfig=kubeconfig apply -f manifests/canary-deployment.yml"                 
        }
      }
    }
  }
}
