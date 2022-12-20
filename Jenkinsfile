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
}
stages{
    stage("build"){
        steps{ 
           sh "mvn -s settings.xml -DskipTests install" 
        }      
    }
    post {
        always {
            echo "Now Archiving"
            archiveArtifacts artifacts: '*/**.war', onlyIfSuccessful: true
        }
      }    
    stage("Test"){
        steps{
           sh "mvn -s settings.xml test"
       }
    }  
    stage("Checkstyle qualitycode"){
        steps{
           sh "mvn -s settings.xml checkstyl:checkstyle"
        }
    }
  } 
}
     


