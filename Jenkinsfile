library identifier: 'jenkins-shared_library@main',
        retriever: modernSCM([$class: 'GitSCMSource',
        remote: 'https://github.com/bogdanagache/jenkins-shared_library.git'])

pipeline {
  agent any
  parameters {
    booleanParam(name: 'RC', defaultValue: false, description: 'Is this a Release Candidate?')
  }
  environment {
    VERSION = '0.1.0'
    VERSION_RC = 'rc.2'
  }
  stages {
    stage('Audit  Tools') {
      steps {
        auditTools()
      }
    }
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
        echo "Building version: ${VERSION}"
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
      slackSend channel: '#builds',
      color: 'good',
      message: "Build version: ${VERSION} was SUCCESSFUL: ${currentBuild.fullDisplayName}."
    }
    failure {
      /* groovylint-disable-next-line DuplicateStringLiteral */
      slackSend channel: '#builds',
      color: 'danger',
      message: "Building version: ${VERSION} has FAILED: ${currentBuild.fullDisplayName}."
    }
  }
}
