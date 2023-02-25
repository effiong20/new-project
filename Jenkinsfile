pipeline{
    agent any
    tools{
       maven "MVN8" 
       jdk    "JDK8" 
    }
     options{timeout(time: 1, unit: 'HOURS') }
     environment{
      SNAP_REPO="snap-hosted"
      NEXUS_USER= "admin"
      NEXUS_PASS= "admin123"
      RELEASE_REPO="proxy-maven"
      CENTRAL_REPO="maven-hosted"
      NEXUS_GRP_REPO="group-maven"
      NEXUSIP= "172.31.24.201"
      NEXUSPORT= 8081
      NEXUS_LOGIN= "nexus1"
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
    