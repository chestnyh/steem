pipeline {
    agent any
    stages {
        stage('Build') {
            failFast true
            parallel {
                stage('Build tests') {
                    agent any
                    steps {
                        sh 'ciscripts/triggertests.sh'
                        step([$class: 'CoberturaPublisher', autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: '**/cobertura/coverage.xml', failUnhealthy: false, failUnstable: false, maxNumberOfBuilds: 0, onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false])
                    }
                }
                stage('Build docker image') {
                    agent any
                    steps {
                        sh 'ciscripts/triggerbuild.sh'
                    }
                }
            }
        }
      }
    }
    post {
        success {
            sh 'ciscripts/buildsuccess.sh'
    }
    failure {
            sh 'ciscripts/buildfailure.sh'
            slackSend (color: '#ff0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }
  }
}