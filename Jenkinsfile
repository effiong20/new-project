pipeline{
    agent any
    tools{
       maven "mvn" 
       jdk    "java" 
    }
     options {timeout(time: 1, unit: 'HOURS') }
     environment{
      SNAP_REPO=
      NEXUS_USER= "admin"
      NEXUS_PASS= "admin123"
      RELEASE_REPO=
      CENTRAL_REPO=
      NEXUS_GRP_REPO=
      NEXUSIP=
      NEXUSPORT= 8081
  }
  stages{
     stage("src code pull from git"){
        steps{
          git branch: 'ci-jenkins',
          url: 'https://github.com/effiong20/new-project.git' 
        }
     }
      stage("building stage"){
         steps{
            sh script: "mvn clean install -DskipunitTest"
        }
      }
      stage("archivethe artifact"){
        steps{
          archiveArtifacts artifacts: '**/*.war'
        }
      }      
      stage("junit report"){
        steps{
          junit testResults: "target/surefire-reports/*.xml"  
        }
      }      
  }
}
    