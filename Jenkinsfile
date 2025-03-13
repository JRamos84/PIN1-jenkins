pipeline {
  agent any

  options {
    timeout(time: 2, unit: 'MINUTES')
  }

  environment {
    DOCKER_IMAGE_NAME = "joseph0001519/webapp"
    IMAGE_TAG = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}".replaceAll('/', '-') // Evita caracteres inválidos en Docker
  }

  stages {
    stage('Login to Docker Hub') {
      steps {
        script {
          withCredentials([usernamePassword(credentialsId: 'mundose-hub', 
                                           passwordVariable: 'DOCKER_HUB_PASSWORD', 
                                           usernameVariable: 'DOCKER_HUB_USERNAME')]) {
            sh "echo ${DOCKER_HUB_PASSWORD} | docker login -u ${DOCKER_HUB_USERNAME} --password-stdin"
          }
        }
      }
    }

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
            withCredentials([usernamePassword(credentialsId: 'mundose-hub', 
                                           passwordVariable: 'DOCKER_HUB_PASSWORD', 
                                           usernameVariable: 'DOCKER_HUB_USERNAME')]) {
          
            sh """
              echo ${DOCKER_HUB_PASSWORD} | docker login -u ${DOCKER_HUB_USERNAME} --password-stdin
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
