pipeline{
    agent any
    tools{
       maven "maven" 
       jdk    "jdk11" 
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
      NEXUS_LOGIN= "sample1"
      
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
    