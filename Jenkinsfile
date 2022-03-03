pipeline {
  agent any 
  tools {
    maven 'maven'
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
    
       stage('detect-secrets'){
            steps{
                sh 'detect-secrets scan > .secret.json'
            }
         }

        stage ('OWASP Dependency-Check') {
            steps {
                dependencyCheck additionalArguments: ''' 
                    -o "./" 
                    -s "./"
                    -f "ALL" 
                    --prettyPrint''', odcInstallation: 'OWASP-DC'

                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
            }
        } 
       
       stage('Build'){
            steps{
                sh 'mvn clean package'
            }
         }    
    
        stage('SonarQube analysis') {
            steps{
                withSonarQubeEnv('sonarqube') { 
                sh "mvn sonar:sonar"
               }
            }
        }    
/* 
    stage ('Deploy-To-Tomcat') {
            steps {
              sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war tomcat-vm@52.168.22.100:/prod/apache-tomcat-8.5.76/webapps/webapp.war'
              }      
           }       
        }
          
    stage ('DAST') {
      steps {
        sshagent(['zap']) {
          sh 'ssh -o  StrictHostKeyChecking=no zap-vm@40.117.122.39 "sudo docker run -t owasp/zap2docker-stable zap-baseline.py -t http://52.168.22.100:8080/webapp/" || true'
        }
      }
    }
*/    
  }
}
