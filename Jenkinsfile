pipeline {
  agent {
    kubernetes {
      //label 'default-ci'
      defaultContainer 'jnlp'
      yamlFile "deployment/jenkins-agent-pod.yaml"
    }
}
  stages {
    stage('Build') {
      steps {
        container('maven') {
          sh 'mvn clean install -DskipTests'
        }
      }
    }

    stage('Test') {
      steps {
        container('maven') {
          sh 'mvn test'
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        container('docker') {
          sh "docker images"
        }
      }
    }
  }
}