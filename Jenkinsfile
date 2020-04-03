pipeline {
  agent any
  stages {
    stage('clean and build') {
      steps {
        bat 'mvn -B clean'
      }
    }

    stage('Windows Testing') {
        steps {
            script {
                try {
                    bat 'mvn test'
                }
                catch(e) {
                    build_ok = false
                    echo e.toString()
                }
            }
        }
    }

    stage('create package') {
          when {
            branch 'master'
            build_ok
          }
          post {
            success {
              echo 'Now Archiving...'
              archiveArtifacts 'target/*.jar'
            }

          }
          steps {
            bat 'mvn package verify'
          }
    }



    stage('Reporting') {
        parallel {
            stage('Record Jacoco') {
              steps {
                jacoco()
              }
            }

            stage('Record Junit') {
              steps {
                junit 'target/surefire-reports/*.xml'
              }
            }

            stage('Capture PMD') {
              steps {
                pmd()
              }
            }

            stage('Capture findbugs') {
              steps {
                findbugs()
              }
            }


        }
    }
    stage('Clean environment') {
      steps {
        bat 'mvn clean'
      }
    }

  }
}