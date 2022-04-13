pipeline {
  agent any 
/*  
  tools {
    maven 'maven'
  }
*/  
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }
/*     
       stage('detect-secrets'){
            steps{
                sh 'detect-secrets scan > .secret.json'
                sh 'cat .secret.json'
            }
         }
*/
        stage ('OWASP Dependency-Check') {
            steps {
                  sh 'wget https://raw.githubusercontent.com/Yassine499/DevSecOps/main/owasp-dependency-check.sh'
                  sh 'chmod +x owasp-dependency-check.sh'
                  sh 'bash owasp-dependency-check.sh'
            }
        } 
       
       stage('Build'){
            steps{
                sh 'mvn clean package'
            }
         }    
/*    
        stage('SonarQube analysis') {
            steps{
                withSonarQubeEnv('sonarqube') { 
                sh "mvn sonar:sonar"
               }
            }
        }    

    stage ('Deploy-To-Tomcat') {
            steps {
              sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war tomcat-vm@52.186.21.203:/prod/apache-tomcat-8.5.76/webapps/webapp.war'
              }      
           }       
        }
    
       stage('Nikto'){
            steps{
                sh 'nikto -h http://52.186.21.203:8080/webapp/ -Plugins robots -output nikto-report.xml'
            }
         }
    
    stage ('DAST') {
      steps {
        sshagent(['zap']) {
          sh 'ssh -o  StrictHostKeyChecking=no zap-vm@52.186.20.101 "sudo docker run -t owasp/zap2docker-stable zap-baseline.py -t http://52.186.21.203:8080/webapp/" || true'
        }
      }
    }
    
    stage('openvas'){
        steps{
                sh 'sudo docker exec openvas omp -S 6c698f59-810f-40d7-b1bc-5986170dd246'
            }
         }       
  */  
    
  }
}
