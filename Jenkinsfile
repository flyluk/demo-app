pipeline {

  agent {
        docker { 
            image 'maven:local-user' 
            args '-v $HOME/.m2:/home/jenkins/.m2'
            }
    }

    
  stages {
     stage ('Artifactory configuration') {
            steps {
                
                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "local-libs-release",
                    snapshotRepo: "local-libs-snapshot"
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )
            }
        }

    stage('Unit Test') {
      steps {
        withMaven ( globalMavenSettingsConfig : "1786ed01-12e2-443f-b072-085572b18289" ) {
        sh 'mvn -B clean test'
        }
      }
    }

    stage('Deploy to Artifactory') {
      steps {
        withMaven ( globalMavenSettingsConfig : "1786ed01-12e2-443f-b072-085572b18289" ) {
        sh 'mvn -B deploy'
        }
      }
    }
  } 
  post {
   
    always {
      //junit '**/TEST*.xml'
      
      //jacoco(
      //      execPattern: 'target/jacoco.exec',
      //      classPattern: 'target/classes/**',
      //      sourcePattern: 'src/**',
      //      inclusionPattern: '**/*.class'
       // )
      
      script {
        
        if(manager.logContains(".*helwqeedddwqelo.*")) {
        manager.addWarningBadge("Thou shalt not use deprecated methods.")
        manager.createSummary("warning.gif").appendText("<h1>You have been warned!</h1>", false, false, false, "red")
        }
        
      }
    }
  }
}