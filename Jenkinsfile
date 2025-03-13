pipeline {
  agent any

  options {
    timeout(time: 2, unit: 'MINUTES')
  }

  environment {
    DOCKER_IMAGE_NAME = "elbuo8/webapp"
    IMAGE_TAG = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}".replaceAll('/', '-') // Evita caracteres inválidos en Docker
  }

  stages {
    stage('Building image') {
      steps {
        sh "docker build -t ${DOCKER_IMAGE_NAME}:${IMAGE_TAG} ."
      }
    }

    stage('Run tests') {
      steps {
        sh "docker run --rm ${DOCKER_IMAGE_NAME}:${IMAGE_TAG} npm test"
      }
    }

    stage('Deploy Image') {
      steps {
        script {
          withDockerRegistry([credentialsId: '28f887e7-720e-44b1-9f43-02a3d982c6db', url: '']) {
            sh """
              docker tag ${DOCKER_IMAGE_NAME}:${IMAGE_TAG} ${DOCKER_IMAGE_NAME}:${env.BRANCH_NAME}-latest
              docker push ${DOCKER_IMAGE_NAME}:${IMAGE_TAG}
              docker push ${DOCKER_IMAGE_NAME}:${env.BRANCH_NAME}-latest
            """
          }
        }
      }
    }
  }
}
