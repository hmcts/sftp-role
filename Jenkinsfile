#!groovy

properties(
  [[$class: 'GithubProjectProperty', projectUrlStr: 'https://github.com/hmcts/sftp-role'],
   pipelineTriggers([[$class: 'GitHubPushTrigger']]),
  [$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '7', numToKeepStr: '10']]
]
)

@Library('Reform') _

node {
  ws('sftp-role') { // This must be the name of the role otherwise ansible won't find the role
    try {
      wrap([$class: 'AnsiColorBuildWrapper', colorMapName: 'xterm']) {
        stage('Checkout') {
          checkout scm
        }

        stage('Start containers') {
          sh '''
          molecule create
        '''
        }

        stage('Run role') {
          sh '''
          molecule converge
        '''
        }

        stage('Check idempotent') {
          sh '''
          molecule idempotence
        '''
        }

        stage('Run tests') {
          sh '''
          molecule syntax
          molecule verify
        '''
        }
      }

    } catch (err) {
      notifyBuildFailure channel: '#devops'
      throw err
    } finally {
      stage('Cleanup') {
        sh '''
          molecule destroy
        '''
      }
      deleteDir()
    }
  }
}
