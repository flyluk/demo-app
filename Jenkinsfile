pipeline {

  agent {
    kubernetes {
      defaultContainer 'maven'
      yaml """\
        apiVersion: v1
        kind: Pod
        metadata:
          labels:
            some-label: some-label-value
        spec:
          containers:
          - name: maven
            image: maven:latest
            command:
            - cat
            tty: true
            env:
            - name: JAVA_HOME
              value: /java/openjdk-16
          - name: busybox
            image: busybox
            command:
            - cat
            tty: true
        """.stripIndent()
    }
  }
    environment {
        JAVA_HOME = '/usr/java/openjdk-16'
    }
    
  stages {
    stage('init') {
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
            releaseRepo: 'lib-release-local',
            snapshotRepo: 'lib-snapshot-local',
    
        )
        
        rtBuildInfo (captureEnv: true)
      }
    }
    stage('Unit Test') {
      steps {
         rtMavenRun (
            // Tool name from Jenkins configuration.
            tool: "Maven",
            opts: "",
            pom: 'pom.xml',
            goals: 'clean test',
            // Maven options.
            resolverId: 'resolver-unique-id',
            deployerId: 'deployer-unique-id',
            // If the build name and build number are not set here, the current job name and number will be used:
        )
      }
    }
    

    stage('SonarQube Scan') {
      steps {
         withSonarQubeEnv ( "SonarQube") {
              sh "mvn sonar:sonar"
         }
         
         timeout(time: 3, unit: 'MINUTES') {
             waitForQualityGate abortPipeline: false
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
            releaseRepo: 'lib-release-local',
            snapshotRepo: 'lib-snapshot-local',
    
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
      junit '**/TEST*.xml'
      
      jacoco(
            execPattern: 'target/jacoco.exec',
            classPattern: 'target/classes/**',
            sourcePattern: 'src/**',
            inclusionPattern: '**/*.class'
      )
      
      script {
        if(manager.logContains(".*helwqeedddwqelo.*")) {
        manager.addWarningBadge("Thou shalt not use deprecated methods.")
        manager.createSummary("warning.gif").appendText("<h1>You have been warned!</h1>", false, false, false, "red")
        }
        
      }
    }
  }
}
