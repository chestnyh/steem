#!groovy
pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        parallel ( "Build tests":
        {
          script {
            def result = sh returnStatus: true, script: 'ciscripts/triggertests.sh'
            if (result != 0) {
              echo '[FAILURE] Failed to build'
              currentBuild.result = 'FAILURE'
              error 'Aborting due to failure'
              sh "exit ${result}"
            }
          }
          step([$class: 'CoberturaPublisher', autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: '**/cobertura/coverage.xml', failUnhealthy: false, failUnstable: false, maxNumberOfBuilds: 0, onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false])
        },
        "Build docker image": {
          script {
            def result = sh returnStatus: true, script: 'ciscripts/triggerbuild.sh'
            if (result != 0) {
              echo '[FAILURE] Failed to build'
              currentBuild.result = 'FAILURE'
              error 'Aborting due to failure'
              sh "exit ${result}"
            }
          }
        })
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
