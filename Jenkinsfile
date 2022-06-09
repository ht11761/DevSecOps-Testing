pipeline {
  agent any 
  tools {
    maven 'Maven'
  }
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }

   stage ('Check-Git-Secrets') {
      steps {
        sh 'rm trufflehog || true'
        sh 'docker run --rm gesellix/trufflehog --json https://github.com/ht11761/DevSecOps-Testing.git > trufflehog'
        sh 'cat trufflehog'
      }
    }
   
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
   

    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war tunght@172.16.128.142:/tomcat/apache-tomcat-8.5.79/webapps/webapp.war'
              }      
           }       
    }
      }
    }
    
