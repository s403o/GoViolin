pipeline {
  agent any
  stages {
    stage('git checkout') {
      steps {
        git(url: 'https://github.com/s403o/GoViolin.git', branch: '**', changelog: true, poll: true, credentialsId: 'myGit')
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          try {
            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'myDocker', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]) {
              sh '''
                docker build -t s403o/goapp .
              '''
            }
          }
          catch(e) {
            currentBuild.result = "FAILED"
            throw e
          }
        }
      }
    }

    stage('Push Image To Dockerhub') {
      steps {
        withCredentials(bindings: [[$class: 'UsernamePasswordMultiBinding', credentialsId: 'myDocker', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]) {
          sh '''
              docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
              docker push s403o/goapp
	        '''
        }

      }
    }
  }
  post {
    failure {
        mail to: 'eslam.adel.me@gmail.com',
             subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
             body: "Something is wrong with ${env.BUILD_URL}"
    }
  }
}
