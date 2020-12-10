pipeline {
    agent any
    environment {
        RELEASE = '20.04'
    }
    stages {
        stage('Git checkout') {
            agent any
            checkout([
            $class: 'GitSCM',
            branches: [[name: '*/main']],
            doGenerateSubmoduleConfigurations: false,
            extensions: [],
            submoduleCfg: [],
            userRemoteConfigs:
             [[credentialsId: '', url: 'https://github.com/bogdanagache/jgsu-spring-petclinic']]])
        }
        stage ('Building jgsu-spring-petclinic') {
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
        stage('Testing') {
            steps {
                echo "Testing. I can see release ${RELEASE}, but not log level ${LOG_LEVEL}"
            }
        }
    }
}
