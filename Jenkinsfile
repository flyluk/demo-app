pipeline {

  agent {
        docker { 
            image 'maven:local-user' 
            args '-v $HOME/.m2:/home/jenkins/.m2'
            }
    }
  stages {
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