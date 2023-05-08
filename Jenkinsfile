pipeline {
  environment {
    dockerimagename = "narayanacharan/react-app-00"
    dockerImage = ""
  }
  agent none
  stages {
    stage('Checkout Source') {
      agent {
                docker {
                    image 'narayanacharan/jenkins-slave:2.0'
                    label 'java-docker-slave'
                    args '-v $HOME/.kube/config:/root/.kube/config'
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
        withKubeConfig([credentialsId: 'kubeconfig', serverUrl: 'https://nextopsaks01-dns-g49pt1dh.hcp.eastus.azmk8s.io']) {
            kubernetesDeploy(
            kubeconfigId: 'kubeconfig',
            configs: 'hellowhale.yml',
            enableConfigSubstitution: true,
            recreate: true
          )
        }  
      }
    }
  }
}
