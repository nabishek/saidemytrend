pipeline {
  agent any
  environment {
    PATH = "/opt/maven/bin:$PATH"
  }

  stages {
    stage ('build') {
     steps {
       sh 'mvn clean deploy'
      }
    }

  stage('SonarQube analysis') {
   environment {
     scannerHome = tool 'sonarqube-scanner'                                
   }
   steps {
    withSonarQubeEnv('SQ-Server') {
     sh "${scannerHome}/bin/sonar-scanner"
        }
      }
    }
  }
}
