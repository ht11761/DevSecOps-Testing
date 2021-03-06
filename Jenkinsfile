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
    
    stage ('Source Composition Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/ht11761/DevSecOps-Testing/main/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        
      }
    }
    
    stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar'
          sh 'cat target/sonar/report-task.txt'
        }
      }
    }
    stage("QualityGate") {
            steps {
                waitForQualityGate abortPipeline: true
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
    
    
    stage ('DAST') {
      steps {
        sshagent(['zap']) {
         sh 'ssh -o  StrictHostKeyChecking=no tunght@172.16.128.141 "docker run --rm -t owasp/zap2docker-stable zap-full-scan.py -t http://172.16.128.142:8080/webapp/" || true'
        }
      }
    }
    
  }
}
