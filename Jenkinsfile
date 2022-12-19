pipeline{
    agent any
    tools {
      MAVEN "MAVEN_HOME"    
      JDK    "JAVA_HOME"
    }  
environment {
      SNAP_REPO "MVN-SNAP"
      NEXUS_USER "admin"
      NEXUS_PASS "admin123"
      RELEASE_REPO "MVN-RELEASE"
      CENTRAL_REPO "MVN-DOWNLOADS"
      NEXUS-GRP_REPO "MVN-GROUP"
      NEXUSIP "172.31.74.38"
      NEXUSPORT "8081"
}
 stages{
    stage("build"){
        step{ 
           sh "mvn -s settings.xml -DskipTest install" 
        }
    }
 }

}
