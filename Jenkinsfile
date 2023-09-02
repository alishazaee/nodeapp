pipeline {
  agent any
  
  environment {
    BUILD_VERSION = "${BUILD_NUMBER}"
    DOCKER_REGISTRY = "alishazaei"
    DOCKER_REPO = "nodeapp"
  }

  stages {

    stage('Install Neccessary Packages') {
      steps {
        script {
            sh 'apt install jq || (apt update && apt install jq)'
        }
      }
    }


    stage('Set version') {
      steps {
        script {

           def VERSION = sh(script:'echo $(cat simple-nodejs/package.json | jq -r .version)-${BUILD_VERSION}',returnStdout: true).trim()

           env.VERSION = VERSION

          sh "echo VERSION Number :  ${env.VERSION} "
        }
      }
    }

    stage('Docker login to Docker Hub') {
      steps {
        script {
          withCredentials([usernamePassword(credentialsId: '6140a374-aa4e-4fa0-9187-aef779023cc8	', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
            sh "docker login -u $USERNAME -p $PASSWORD"
          }
        }
      }
    }

    stage('Build and push Docker image') {
      steps {
        script {
            sh "cd simple-nodejs && docker build -t ${DOCKER_REGISTRY}/${DOCKER_REPO}:v${VERSION} -f Dockerfile ."
            sh "docker push ${DOCKER_REGISTRY}/${DOCKER_REPO}:v${VERSION}"

        }
      }
    }








  }
}