pipeline {
  agent {
    kubernetes {
      //label 'default-ci'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  containers:
    - name: maven
      image: maven:latest
      command:
      - cat
      tty: true
    - name: docker
      image: docker:latest
      command:
      - cat
      tty: true
      volumeMounts:
        - name: docker
          mountPath: /var/run/docker.sock
  volumes:
    - name: docker
      hostPath:
        path: /var/run/docker.sock
"""
}
   }
  stages {
    stage('Build') {
      steps {
        container('maven') {
          sh 'mvn clean package -DskipTests'
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