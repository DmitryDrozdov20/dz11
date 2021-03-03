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
        sh 'docker build -f Dockerfile -t 35.194.237.167:8123/webapp:prod .'
        sh 'docker push 35.194.237.167:8123/webapp:prod'
      }
    }

    stage('Pull docker image and run container on production environment') {
      steps {
        //Using SSH Agent plugin with private key
        sshagent(['c599679c-0d65-4c66-8d0a-49d266b6dfaa']) {
          sh '''ssh -o StrictHostKeyChecking=no root@34.121.239.27 << EOF
          docker pull mas0lik/prod-webapp:prod
          docker run --rm --name prod-webapp-deployed -d -p 8888:8080 mas0lik/prod-webapp:prod
EOF'''
        }
      }
    }
  }
}