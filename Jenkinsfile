def previousBuildNumber = currentBuild.getPreviousBuild()?.getNumber()
pipeline {
  environment {
    dockerimagename = "narayanacharan/nginx-app:${BUILD_NUMBER}"
    dockerImage = ""
  }
  agent any
  stages {
    stage('Build') {
            steps {
                echo "current build number is ${BUILD_NUMBER}"
              echo "previous build number is ${previousBuildNumber}"
            }
        }
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
            dockerImage.push("${BUILD_NUMBER}")
          }
        }
      }
    }
   
    stage('Deploy to Kubernetes') {
      steps {
        withCredentials([string(credentialsId: 'kubeconfig-secret', variable: 'KUBECONFIG')]) {
                    writeFile(file: 'kubeconfig', text: KUBECONFIG)
                    sh "kubectl --kubeconfig=kubeconfig apply -f hellowhale.yml"                 
        }
      }
    }
  }
}
