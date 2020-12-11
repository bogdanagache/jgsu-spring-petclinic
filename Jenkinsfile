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
    environment {
      /* groovylint-disable-next-line UnnecessaryGetter */
      VERSION_SUFFIX = getVersionSuffix()
    }
    success {
      slackSend channel: '#builds',
      color: 'good',
      message: "Building version: ${VERSION} with suffix: ${VERSION_SUFFIX} was SUCCESSFUL: ${currentBuild.fullDisplayName}."
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

void auditTools() {
  sh '''
    git version
    docker version
    dotnet --list-sdks
    dotnet --list-runtimes
  '''
}
