pipeline {
  agent any
  
  environment {
    BUILD_VERSION = "${BUILD_NUMBER}"
    DOCKER_REGISTRY = "alishazaei"
    DOCKER_REPO = "nodeapp"
    ANSIBLE_SERVER="146.19.212.14"
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



        stage('Deploy') {
            steps {
                // Copy Ansible files to the server
                sshagent(['cd294e96-9019-40dc-9c01-315a49a609f3']) {
                    sh 'ssh -o StrictHostKeyChecking=no root@${ANSIBLE_SERVER} "mkdir -p /root/Ansible"'
                    sh "scp -r Ansible/* root@${ANSIBLE_SERVER}:/root/Ansible"
                withCredentials([sshUserPrivateKey(credentialsId: 'deployment-server', keyFileVariable: 'keyfile', usernameVariable: 'username')]) {
                    sh "mkdir -p /root/.ssh && scp keyfile root@${ANSIBLE_SERVER}:/root/.ssh/id_rsa"
                }
                
                }
                
                // Execute Ansible playbook on the server
                // sshagent(['cd294e96-9019-40dc-9c01-315a49a609f3']) {
                //     sh 'ssh user@your-server "cd /path/to/ansible && ansible-playbook playbook.yml"'
                // }
            }




        }



  }
}