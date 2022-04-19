pipeline {
  agent any 

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
                sh 'mkdir -p mkdir reports/detect-secrets'
                sh '/usr/local/bin/detect-secrets  scan > reports/detect-secrets/secrets.baseline'
            }
         }

        stage ('OWASP Dependency-Check') {
            steps {
                  sh 'mkdir -p reports/owasp-dc || true'
                  sh 'rm owasp-dependency-check.sh || true'
                  sh 'wget https://raw.githubusercontent.com/Yassine499/DevSecOps/main/owasp-dependency-check.sh'
                  sh 'chmod +x owasp-dependency-check.sh'
                  sh 'bash owasp-dependency-check.sh || true'
            }
        } 
       
       stage('Build'){
            steps{
                sh 'mvn clean package'
            }
         }    
   
        stage('SonarQube Analysis') {
            steps{
                withSonarQubeEnv('sonarqube') { 
                sh "mvn sonar:sonar"
               }
            }
        }    

    stage ('Deploy-To-Tomcat') {
            steps {
                  sh  'sudo cp target/*.war /opt/tomcat10/webapps/webapp.war'     
           }
        }
    
    stage ('DAST') {
      steps {
            sh 'mkdir -p reports/owasp-zap' /* xml format didn't work */
            sh 'docker run -v $(pwd)/reports/owasp-zap/:/zap/wrk/:rw -t owasp/zap2docker-stable zap-baseline.py -t http://10.0.1.19:8085/webapp/ -r owasp-zap-report || true'
      }
    }
/*    
    stage('openvas'){
        steps{
                sh 'sudo docker exec openvas omp -S 6c698f59-810f-40d7-b1bc-5986170dd246'
            }
         }       
  */  
    
  }
}
