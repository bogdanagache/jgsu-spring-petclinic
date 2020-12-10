pipeline {
  agent any
  environment {
    RELEASE = 'v1'
  }
  stages {
    stage('Checkout') {
      steps {
        checkout([
          $class: 'GitSCM',
          branches: [[name: '*/main']],
          doGenerateSubmoduleConfigurations: false,
          extensions: [],
          submoduleCfg: [],
          userRemoteConfigs:
          [[credentialsId: '', url: 'https://github.com/bogdanagache/jgsu-spring-petclinic']]])
      }
    }
    stage('Build') {
      steps {
        // Shell build step
        sh '''
        ./mvnw package
        '''
        archiveArtifacts allowEmptyArchive: false,
        artifacts: 'target/*.jar',
        caseSensitive: true,
        defaultExcludes: true,
        fingerprint: false,
        onlyIfSuccessful: false
        // JUnit Results
        junit 'target/surefire-reports/*.xml'
      }
    }
  }
  post {
    success {
      archiveArtifacts 'test-results.txt'
      slackSend channel: '#builds',
      color: 'good',
      message: "Release ${env.RELEASE}, success: ${currentBuild.fullDisplayName}."
    }
    failure {
      slackSend channel: '#builds',
      color: 'danger',
      message: "Release ${env.RELEASE}, FAILED: ${currentBuild.fullDisplayName}."
    }
  }
}
