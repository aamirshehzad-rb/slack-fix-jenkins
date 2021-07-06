pipeline {
  options {
    ansiColor('xterm')
  }
  agent {
      label 'edly-ecs-slave-agent'
  }
  environment {
     BUILD_USER = getBuildUser()
  }
  stages {
    stage('Send Start Notification') {
      steps {
        slackSend (channel: env.SLACK_CHANNEL, tokenCredentialId: 'slack', teamDomain: env.SLACK_ORG, color: '#FFFF00', message: "*STARTED:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} for ${env.BUILD_FOR} by ${env.BUILD_USER}\n More info at: ${env.BUILD_URL}")
      }
    }
    stage('test-stage'){
      steps {
        sh """
          echo 'test-job'
          echo ${env.BUILD_USER}
        """
      }
    }
  }
  post {
    success {
       slackSend (channel: env.SLACK_CHANNEL, tokenCredentialId: 'slack', teamDomain: env.SLACK_ORG, color: '#00FF00', message: "*SUCCESSFUL:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} for ${env.BUILD_FOR} by ${env.BUILD_USER}\n More info at: ${env.BUILD_URL}")
    }
    failure {
       slackSend (channel: env.SLACK_CHANNEL, tokenCredentialId: 'slack', teamDomain: env.SLACK_ORG, color: '#FF0000', message: "*FAILED:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} for ${env.BUILD_FOR} by ${env.BUILD_USER}\n More info at: ${env.BUILD_URL}")
    }
    always {
      cleanWs()
    }
  }
}

def getBuildUser() {
   def job = Jenkins.getInstance().getItemByFullName(env.JOB_BASE_NAME, Job.class)
   def build = job.getBuildByNumber(env.BUILD_ID as int)
   def username = build.getCause(Cause.UserIdCause).getUserName()
   return username
}
