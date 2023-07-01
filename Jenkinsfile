pipeline {
  environment {
    dockerimage1name = "abdoman/frontend"
    dockerimage2name = "abdoman/backend"
    dockerImage1 = ""
    dockerImage2 = ""
  }
  agent {
    label 'docker' 
  }
  stages {

    stage('Docker node test') {
      agent {
        docker {
          // Set both label and image
          label 'docker'
          image 'node:7-alpine'
          args '--name docker-node' // list any args
        }
      }
      steps {
        // Steps run in node:7-alpine docker container on docker agent
        sh 'node --version'
      }
    }
    stage('Docker maven test') {
      agent {
        docker {
          // Set both label and image
          label 'docker'
          image 'maven:3-alpine'
        }
      }

    stage('Checkout Source') {
      steps {
        git 'https://github.com/Azahaby/SRE.git'
      }
    }
    stage('Build image') {
      steps{
        script {
          dockerImage1 = docker.build dockerimage1name
          dockerImage2 = docker.build dockerimage2name
        }
      }
    }
    stage('Pushing Image 1') {
      environment {
               registryCredential = 'dockerhub-credentials'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage1.push("latest")
          }
        }
      }
    }
    stage('Pushing Image 2') {
      environment {
               registryCredential = 'dockerhub-credentials'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage2.push("latest")
          }
        }
      }
    }
    stage('Deploying React.js container to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deployment.yml", "service.yml")
        }
      }
    }
  }
}