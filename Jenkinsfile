pipeline {
    agent any
    tools {
      maven "MAVEN3"    
      jdk   "JDK3"
    }  
environment {
      SNAP_REPO = "MVN-SNAP"
      NEXUS_USER = "admin"
      NEXUS_PASS = "admin123"
      RELEASE_REPO = "MVN-RELEASE"
      CENTRAL_REPO = "MVN-DOWNLOADS"
      NEXUS_GRP_REPO = "MVN-GROUP"
      NEXUSIP = "172.31.76.170"
      NEXUSPORT = "8081"
      NEXUS_LOGIN = "nexus-key"
      SONARSERVER = "sonarserver"
      SONARSCANNER = "sonarscanner"
      registryCredentials = "ecr:us-east-1:awscred"
      appRegistry = "887592648157.dkr.ecr.us-east-1.amazonaws.com/vprofilejob"
      vprofileRegistry = "https://887592648157.dkr.ecr.us-east-1.amazonaws.com"
}
stages{
    stage("Build"){
        steps{ 
           sh "mvn -s settings.xml -DskipTests install" 
        }      
    
        post {
           success {
               echo "Now Archiving"
               archiveArtifacts artifacts: '*/**.war'
        }
        }
      }    
    stage("Test"){
        steps{
           sh "mvn -s settings.xml test"
       }
    }  
    stage("Checkstyle Qualitycode"){
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
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    stage("Artifact Upload to nexus") {
           steps {
        nexusArtifactUploader(
        nexusVersion: 'nexus3',
        protocol: 'http',
        nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
        groupId: 'QA',
        version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
        repository: "${RELEASE_REPO}",
        credentialsId: "${NEXUS_LOGIN}",
        artifacts: [
            [artifactId: "vproapp",
             classifier: '',
             file: 'target/vprofile-v2.war',
             type: 'war']
        ]
     )
    }
    }  
    stage("build-image"){
        steps{
            script{
                dockerimage=docker.build ( appRegistry + ":$BUILD_NUMBER", "./Docker-files/app/Dockerfile")
            }
        }
    }
    stage("push-image"){
        steps{
            script{
                docker.withRegistry(vprofileRegistry  registryCredentials){
                    dockerimage.push("$BUILD_NUMBER")
                    dockerimage.push("latest")
                }
            }
        }
    }
}
 }
