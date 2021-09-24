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
      image: maven:3.6.3-jdk-11
      command:
      - cat
      tty: true
      volumeMounts:
        - name: m2
          mountPath: /root/.m2/repository
    - name: docker
      image: docker:latest
      command:
      - cat
      tty: true
      volumeMounts:
        - name: docker
          mountPath: /var/run/docker.sock
  volumes:
    - name: m2
      hostPath:
        path: /root/.m2/repository
        type: DirectoryOrCreate
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