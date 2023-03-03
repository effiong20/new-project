pipeline{
    agent any
    tools{
       maven "maven" 
       jdk    "jdk11" 
       nodejs "nodejs"
    }
     options{timeout(time: 1, unit: 'HOURS') }
     environment{
      SNAP_REPO="snaprepo"
      NEXUS_USER= "admin"
      NEXUS_PASS= "admin123"
      RELEASE_REPO="proxyrepo"
      CENTRAL_REPO="hostedrepo"
      NEXUS_GRP_REPO="grouprepo"
      NEXUSIP= "172.31.24.78"
      NEXUSPORT= "8081"
      NEXUS_LOGIN= "admin"
      SONARSERVER="sonarserver"
      SONARSCANNER="sonarscanner"
  }
  stages{
      
       stage("building stage"){
         steps{
            sh "mvn -s settings.xml -DskipTests install"
        }
     
         post {
          always {
            echo "Archive the Artifact"
            archiveArtifacts artifacts: '*/**.war', onlyIfSuccessful: true
         }
       }
    }
       stage("testjob"){
          steps{
            sh "mvn -s settings.xml test"
          }
       }
       stage("checkstyle"){
         steps{
          sh "mvn -s settings.xml checkstyle:checkstyle"
         }
       }
    stage("Sonar Scanner"){
       environment {
             scannerHome = tool "${SONARSCANNER}"
          }
       steps {
            withSonarQubeEnv("${SONARSERVER}") {
               sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
        }
     }
  }       
  stage("Quality Gate") {
          steps {
            timeout(time: 1, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
        }
      }
    }
    stage("artifactupload"){
        steps{
        nexusArtifactUploader(
        nexusVersion: 'nexus3',
        protocol: 'http',
        nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
        groupId: 'QA',
        version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
        repository: '${CENTRAL_REPO}',
        credentialsId: '${NEXUS_LOGIN}',
        artifacts: [
            [artifactId: "vproapp",
             classifier: '',
             file: 'target/vprofile-v2.war',
             type: 'war']
        ]
     )
        }

    }
}
}
