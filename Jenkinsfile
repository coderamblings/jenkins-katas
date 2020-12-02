pipeline {
  agent any
  stages {
    stage('clone down') {
      steps {
        sh 'echo "Hello, clone world!"'
      }
    }

    stage('Parallel execution') {
      parallel {
        stage('Say Hello') {
          steps {
            sh 'echo "Hello, cruel world!"'
          }
        }

        stage('Build app with gradle') {
          options {
          skipDefaultCheckout true
          }   
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
	    unstash(name: 'code', includes: '.git')
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
          }
        }

      }
    }

  }
}
