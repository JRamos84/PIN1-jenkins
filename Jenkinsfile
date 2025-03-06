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
        sh "sudo docker build -t ${DOCKER_IMAGE_NAME}:${IMAGE_TAG} ."
      }
    }

    stage('Run tests') {
      steps {
        sh "sudo docker run --rm ${DOCKER_IMAGE_NAME}:${IMAGE_TAG} npm test"
      }
    }

    stage('Deploy Image') {
      steps {
        script {
          withCredentials([usernamePassword(credentialsId: 'docker-hub-mundose', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
            sh "echo ${DOCKER_HUB_PASSWORD} | docker login -u ${DOCKER_HUB_USERNAME} --password-stdin"
            
            // Etiquetar la imagen antes de subirla
            sh "sudo docker tag ${DOCKER_IMAGE_NAME}:${IMAGE_TAG} ${DOCKER_IMAGE_NAME}:${env.BRANCH_NAME}-latest"
            
            // Subir la imagen a Docker Hub
            sh "sudo docker push ${DOCKER_IMAGE_NAME}:${IMAGE_TAG}"
            sh "sudo docker push ${DOCKER_IMAGE_NAME}:${env.BRANCH_NAME}-latest"
          }
        }
      }
    }
  }
}
