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
        withMaven ( mavenSettingsConfig : "MavenGlobalSettings" ) {
        sh 'mvn -B clean test'
        }
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