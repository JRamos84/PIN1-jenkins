pipeline {
  agent any

  options {
    timeout(time: 2, unit: 'MINUTES')
  }

  environment {
    ARTIFACT_ID = "elbuo8/webapp:${env.BUILD_NUMBER}"
  }
   stages {
   stage('Building image') {
      steps{
          sh '''
          docker build -t testapp .
             '''  
        }
    }
  
  
    stage('Run tests') {
      steps {
        sh "docker run testapp npm test"
      }
    }
   stage('Deploy Image') {
     steps {
    script {
        withCredentials([usernamePassword(credentialsId: 'docker-hub-mundose', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
            sh "echo ${DOCKER_HUB_PASSWORD} | docker login -u ${DOCKER_HUB_USERNAME} --password-stdin"
            sh "docker push ${DOCKER_IMAGE_NAME}:${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
            sh "docker tag ${DOCKER_IMAGE_NAME}:${env.BRANCH_NAME}-${env.BUILD_NUMBER} ${DOCKER_IMAGE_NAME}:${env.BRANCH_NAME}-latest"
            sh "docker push ${DOCKER_IMAGE_NAME}:${env.BRANCH_NAME}-latest"
        }
    }
      }
    }
}



    
  

