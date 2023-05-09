pipeline {
  agent any
<<<<<<< HEAD

  stages {
    stage('Deploy Blue') {
      steps {
        // Deploy the application to the "blue" environment
        kubectl apply -f blue-deployment.yaml
        kubectl apply -f ingress-blue.yaml
=======
  stages {
    stage('Checkout Source') {
      agent {
                docker {
                    image 'narayanacharan/jenkins-slave:7.0'
                    label 'java-docker-slave'                    
                }
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
>>>>>>> a7ac0babee85f304288ec30074a6fbd2ca12317c
      }
    }

    stage('Deploy Canary') {
      steps {
<<<<<<< HEAD
        // Deploy the new version of the application to a canary environment
        kubectl apply -f canary-deployment.yaml
        kubectl apply -f ingress-canary.yaml
      }
    }

    stage('Test Canary') {
      steps {
        // Run tests on the canary environment
        // If tests fail, roll back to the previous version
        // If tests pass, continue to the next stage
      }
    }

    stage('Switch Traffic') {
      steps {
        // Gradually switch traffic from the "blue" environment to the canary environment
        // If the canary environment experiences issues, switch traffic back to the "blue" environment
      }
    }

    stage('Deploy Green') {
      steps {
        // Deploy the new version of the application to the "green" environment
        kubectl apply -f green-deployment.yaml
        kubectl apply -f ingress-green.yaml
      }
    }

    stage('Clean Up') {
      steps {
        // Remove the old version of the application and ingress resources
        kubectl delete -f blue-deployment.yaml
        kubectl delete -f green-deployment.yaml
        kubectl delete -f ingress-blue.yaml
        kubectl delete -f ingress-green.yaml
=======
        withCredentials([string(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    writeFile(file: 'kubeconfig', text: KUBECONFIG)
                    sh "kubectl --kubeconfig=kubeconfig apply -f canary-deployment.yml"                 
        }
>>>>>>> a7ac0babee85f304288ec30074a6fbd2ca12317c
      }
    }
  }
}
