
//Get the previous build number
//PREVIOUS_BUILD=$((${CURRENT_BUILD} - 1))

pipeline {
  environment {
    dockerimagename = "narayanacharan/nginx-app:${BUILD_NUMBER}"
    dockerImage = ""
  }
  agent any
  stages {
    // Code checkout from Github
    stage('Checkout Source') {
      // Using docker container as slave with a customized docker images
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
    
    //Building docker images using Dockerfile
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }
    
    //Pushing the docker image to https://hub.docker.com/repository/docker/narayanacharan/nginx-app/
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
    
    //Deployment to blue only when changes detected in master branch, but this pipeline only gets triggered on PR.
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
    
    //Deployment to green with latest version of image
    stage('Deploy Green') {
      steps {
        withCredentials([string(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    writeFile(file: 'kubeconfig', text: KUBECONFIG)
                    // sh "sed -i 's|image:v${PREVIOUS_BUILD}|image:v${CURRENT_BUILD}/g' green-deployment.yml"
                    sh "kubectl --kubeconfig=kubeconfig apply -f ingress.yml"            
                    sh "kubectl --kubeconfig=kubeconfig apply -f green-deployment.yml"                 
        }
      }
    }

    //Deployment to canary with latest version of image
    stage('Deploy Canary') {
      steps {
        withCredentials([string(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    writeFile(file: 'kubeconfig', text: KUBECONFIG)
                    // sh "sed -i 's|image:v${PREVIOUS_BUILD}|image:v${CURRENT_BUILD}/g' canary-deployment.yml"
                    sh "kubectl --kubeconfig=kubeconfig apply -f canary-deployment.yml"                 
        }
      }
    }
  }
}
