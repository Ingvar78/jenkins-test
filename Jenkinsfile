pipeline {
  agent {
    docker {
      image 'nginx:stable'
    }

  }
  stages {
    stage('docker login') {
      steps {
        echo ' ============== docker login =================='
        withCredentials(bindings: [usernamePassword(credentialsId: 'dockerHub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
          sh """
                              docker login -u $USERNAME -p $PASSWORD
                              """
        }

      }
    }

    stage('create docker image') {
      steps {
        echo ' ============== start building image =================='
        dir(path: '.') {
          sh 'docker build -t egerpro/nginx-app:latest .'
        }

      }
    }

    stage('docker push') {
      steps {
        echo ' ============== start pushing image =================='
        sh '''docker push egerpro/nginx-app:latest
                '''
      }
    }

  }
  environment {
    IMAGE_BASE = 'egerpro/nginx-app'
    IMAGE_TAG = "v$BUILD_NUMBER"
    IMAGE_NAME = "${env.IMAGE_BASE}:${env.IMAGE_TAG}"
    IMAGE_NAME_LATEST = "${env.IMAGE_BASE}:latest"
    DOCKERFILE_NAME = 'Dockerfile-pack1'
  }
  options {
    skipStagesAfterUnstable()
    skipDefaultCheckout()
    buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
    timestamps()
  }
}