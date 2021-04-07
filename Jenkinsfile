pipeline {

  agent {
        docker { 
            image 'maven' 
            args '-v $HOME/.m2:/home/jenkins/.m2'
            }
    }
  stages {
    stage('Unit Test') {
      steps {
        sh 'mvn -B clean test'
      }
    }
  }
  post {
   
    always {
      junit '**/TEST*.xml'
      jacoco(execPattern: 'target/jacoco.exec')
      script {
        
        if(manager.logContains(".*helwqeedddwqelo.*")) {
        manager.addWarningBadge("Thou shalt not use deprecated methods.")
        manager.createSummary("warning.gif").appendText("<h1>You have been warned!</h1>", false, false, false, "red")
        }
        
      }
    }
  }
}