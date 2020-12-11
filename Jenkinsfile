/* groovylint-disable-next-line CompileStatic */
library identifier: 'jenkins-shared_library@main',
        retriever: modernSCM([$class: 'GitSCMSource', remote: 'https://github.com/bogdanagache/jenkins-shared_library'])

pipeline {
  agent any
  parameters {
    booleanParam(name: 'RC', defaultValue: false, description: 'Is this a Release Candidate?')
  }
  environment {
    VERSION = '0.1.0'
    VERSION_RC = 'rc.2'
    VERSION_SUFFIX = getVersionSuffix()
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
      environment {
        VERSION_SUFFIX = getVersionSuffix()
      }
      steps {
        echo "Building version: ${VERSION} with suffix: ${VERSION_SUFFIX}"
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
      message: "Build version: ${VERSION} with suffix: ${VERSION_SUFFIX} SUCCESSFUL: ${currentBuild.fullDisplayName}."
    }
    failure {
      /* groovylint-disable-next-line DuplicateStringLiteral */
      slackSend channel: '#builds',
      color: 'danger',
      message: "Building version: ${VERSION} with suffix: ${VERSION_SUFFIX} FAILED: ${currentBuild.fullDisplayName}."
    }
  }
}

String getVersionSuffix() {
  if (params.RC) {
    return env.VERSION_RC
  }
  else {
    return env.VERSION_RC + '+ci.' + env.BUILD_NUMBER
  }
}
