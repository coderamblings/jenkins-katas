pipeline {
  agent any
  environment {
          docker_username = 'jenkinscitrain'
      }
  stages {
    stage('clone down') {
      steps {
        sh 'echo "Hello, clone world!"'
	stash excludes: '.git', name: 'code'
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
	    unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            stash(excludes: '.git', name: 'code')
          }
        }

        stage('test app') {
          options {
          skipDefaultCheckout true
          }
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
	          unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }


      }
    }
    stage('push docker app') {
    environment {
          DOCKERCREDS = credentials('4ce4865e-a8ee-4b49-9586-d5c3c2335421') //use the credentials just created in this stage
    }
    steps {
          unstash 'code' //unstash the repository code
          sh 'ci/build-docker.sh'
          sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin' //login to docker hub with the credentials above
          sh 'ci/push-docker.sh'
    }
    }

  }
}
