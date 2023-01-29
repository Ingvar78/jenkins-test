pipeline {
  agent {
    docker {
      image 'nginx:stable'
    }

  }

    stages {
        stage("import credentials") {
            steps {
                echo " ============== import credentials =================="
                sh """
                if [ ! -s /var/jenkins/credentialImported ]; then
                    curl http://localhost:8080/jnlpJars/jenkins-cli.jar -o jenkins-cli.jar
                    java -jar jenkins-cli.jar -s http://localhost:8080 import-credentials-as-xml "system::system::jenkins" < /var/jenkins/exported-credentials.xml
                    echo 'imported' > /var/jenkins/credentialImported
                fi
                """
            }
        }
        stage("docker login") {
            steps {
                echo " ============== docker login =================="
                withCredentials([usernamePassword(credentialsId: 'dockerHub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh """
                    docker login -u $USERNAME -p $PASSWORD
                    """
                }
            }
        }
        stage("create docker image") {
            steps {
                echo " ============== start building image =================="
                dir ('.') {
            	sh 'docker build -t runout/diploma-test-app:latest . '
                }
            }
        }
        stage("docker push") {
            steps {
                echo " ============== start pushing image =================="
                sh '''
                docker push runout/diploma-test-app:latest
                '''
            }
        }
    }
 
  environment {
    IMAGE_BASE = 'egerpro/nginx-app'
    IMAGE_TAG = "v$BUILD_NUMBER"
    IMAGE_NAME = "${env.IMAGE_BASE}:${env.IMAGE_TAG}"
    IMAGE_NAME_LATEST = "${env.IMAGE_BASE}:latest"
    DOCKERFILE_NAME = 'Dockerfile-pack'
  }
  options {
    skipStagesAfterUnstable()
    skipDefaultCheckout()
    buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
    timestamps()
  }
}