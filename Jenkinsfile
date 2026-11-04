pipeline {
  agent any

  options {
    timeout(time: 10, unit: 'MINUTES')
    buildDiscarder(logRotator(numToKeepStr: '10'))
  }

  stages {
    stage('Build Image') {
      steps {
        sh 'docker build -t mysite:${BUILD_NUMBER} -t mysite:latest .'
      }
    }

    stage('Deploy') {
      steps {
        sh '''
          docker rm -f mysite || true
          docker run -d --name mysite --restart unless-stopped -p 80:80 mysite:latest
        '''
      }
    }

    stage('Smoke Test') {
      steps {
        sh '''
          sleep 3
          curl -f http://localhost:80
        '''
      }
    }
  }

  post {
    success { echo "Deployed build ${BUILD_NUMBER}" }
    failure { echo "Build ${BUILD_NUMBER} failed, check the console output" }
  }
}