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
  }
}
    