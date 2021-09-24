def allModules = [
    "service-one",
    "service-two",
    "api-gateway"
]

pipeline {
    agent {
        kubernetes {
            defaultContainer 'jnlp'
            yamlFile "deployment/jenkins-agent-pod.yaml"
        }
    }

    environment {
        IMAGE_TAG = "${env.GIT_COMMIT}"
        DOCKER_REGISTRY = "eproc"
    }

  /*
  options {
  }
  */

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
            script {
                allModules.each { module ->
                    buildDockerImage(module)
                }
            }
        }
    }

    stage('Deploy Helm Chart') {
        steps {
            script {
                allModules.each { module ->
                    deployHelmChart(module)
                }
            }
        }
    }

  }

  /*
  post {
  }
  */
}

def buildDockerImage(String module) {
    stage("Build Docker Image - ${module}") {
        container('docker') {
            sh "cp ${module}/target/*.jar deployment/docker/app.jar"
            dir('deployment/docker') {
                sh "docker build -t ${DOCKER_REGISTRY}/${module}:${IMAGE_TAG} ."
            }
        }
    }
}

def deployHelmChart(String module) {
    stage("Deploy Helm Chart - ${module}") {
        container('helm') {
            dir('deployment/helm') {
                sh """
                    helm upgrade ${module} eproc-java/ \
                        --install --reset-values --history-max 10 --timeout 30m \
                        --set image.repository=${DOCKER_REGISTRY}/${module} \
                        --set image.tag=${IMAGE_TAG}
                    """
            }
        }
    }
}