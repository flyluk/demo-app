pipeline {

  agent {
        docker { 
            image 'maven:local-user' 
            args '-v $HOME/.m2:/home/jenkins/.m2'
            }
    }
    environment {
        JAVA_HOME = '/usr/java/openjdk-16'
    }
    
  stages {
     
    stage('Unit Test') {
      steps {
         withMaven ( globalMavenSettingsConfig : "1786ed01-12e2-443f-b072-085572b18289" ) {
            configFileProvider([configFile(fileId: "1786ed01-12e2-443f-b072-085572b18289", variable: "MAVEN_SETTINGS")]) {
                sh 'mvn --settings ${MAVEN_SETTINGS} -B clean test install'
            }
         }
      }
    }

    stage('SonarQube Scan') {
      steps {
         withSonarQubeEnvMaven ( "SonarQube") {
              sh "mvn sonar:sonar"
         }
         
         timeout(time: 3, unit: 'MINUTES') {
             waitForQualityGate abortPipeline: false
           }
         }
      }
    }
    
    stage('Deploy Artifact') {
      steps {
          rtMavenResolver (
            id: 'resolver-unique-id',
            serverId: 'ARTIFACTORY_SERVER',
            releaseRepo: 'lib-release',
            snapshotRepo: 'lib-snapshot'
          )  
 
        rtMavenDeployer (
            id: 'deployer-unique-id',
            serverId: 'ARTIFACTORY_SERVER',
            releaseRepo: 'local-lib-release',
            snapshotRepo: 'local-lib-snapshot',
    
        )
        
         rtBuildInfo (captureEnv: true)
 
         
         rtMavenRun (
            // Tool name from Jenkins configuration.
            tool: "Maven",
            opts: "-DskipTests=true",
            pom: 'pom.xml',
            goals: 'clean install',
            // Maven options.
            resolverId: 'resolver-unique-id',
            deployerId: 'deployer-unique-id',
            // If the build name and build number are not set here, the current job name and number will be used:
        )
        rtPublishBuildInfo (serverId: 'ARTIFACTORY_SERVER')
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