def registry = 'https://trialhznx0g.jfrog.io/'

pipeline {
  agent any

  environment {
    PATH = "/opt/maven/bin:$PATH"
  }

  stages {
    stage('build') {
      steps {
        echo "---build started---"
        sh 'mvn clean deploy -Dmaven.test.skip=true'
        echo "---build completed---"
      }
    }

    stage('test') {
      steps {
        echo "---test started---"
        sh 'mvn surefire-report:report'
        echo "---test ended---"
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

    stage("Jar Publish") {
     steps {
      script {
       echo '<--------------- Jar Publish Started --------------->'
       def server = Artifactory.newServer url: registry + "/artifactory", credentialsId: "JFrog-cred"
       def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"
       def uploadSpec = """{
            "files": [
              {
                "pattern": "jarstaging/(*)",
                "target": "jfrog-libs-release-local/{1}",
                "flat": "false",
                "props": "${properties}",
                "exclusions": [ "*.sha1", "*.md5"]
         
               }
            }
        }"""
       def buildInfo = server.upload(uploadSpec)
       buildInfo.env.collect()
       server.publishBuildInfo(buildInfo)
       echo '<--------------- Jar Publish Ended --------------->'
    }
   }
  }
 }
} 

