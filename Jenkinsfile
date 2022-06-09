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

   
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }

    }

    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war tunght@172.16.128.142:/home/tunght/tomcat/apache-tomcat-8.5.79/webapps/webapp.war'
              }      
           }       
    }
      }
    }
    

