pipeline {
  agent {
    docker {
      image 'jenkins-agent'
      args  '-v /var/run/docker.sock:/var/run/docker.sock -u 0:0'
    }
  }

  stages {

    stage('Clone git') {
      steps {
        git 'https://github.com/DmitryDrozdov20/boxfuse2.git'
      }
    }

    stage('Build file') {
      steps {
        sh 'mvn package'
      }
    }

    stage('Build docker image') {
      steps {
        sh 'docker login 35.194.237.167:8123 --username admin --password QwErTy123'
        sh 'docker build -f Dockerfile -t 35.194.237.167:8123/webapp:prod .'
        sh 'docker push 35.194.237.167:8123/webapp:prod'
      }
    }

    stage('Pull docker image and run on prod') {
      steps {
        sshagent(credentials: ['root (prod-dz11)']) {
          sh '''ssh -o StrictHostKeyChecking=no root@34.92.53.211 << EOF
          docker pull 35.194.237.167:8123/webapp:prod
          docker run --rm --name prod-webapp -d -p 8888:8080 35.194.237.167:8123/webapp:prod
EOF'''
        }
      }
    }
  }
}