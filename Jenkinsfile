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
                  sh  'cp target/*.war /opt/tomcat10/webapps/webapp.war'     
           }
        }
    
    stage ('OWASP ZAP') {
      steps {
        sshagent(['ubuntu']) {
          sh 'ssh -o  StrictHostKeyChecking=no hunter@10.2.1.8 "mkdir -p reports/owasp-zap"'
          sh 'ssh -o  StrictHostKeyChecking=no hunter@10.2.1.8 "docker run -v $(pwd)/reports/owasp-zap/:/zap/wrk/:rw -t owasp/zap2docker-stable zap-baseline.py -t http://10.0.1.19:8085/webapp/ -r owasp-zap-report || true"'
        }
      }
    }
    
    stage ('OpenVAS') {
      steps {
        sshagent(['ubuntu']) {
          sh 'ssh -o  StrictHostKeyChecking=no hunter@10.2.1.8 "bash /opt/openvas-script"'
        }
      }
    } 
    
    stage ('Uplaod reports to DefectDojo') {
      steps {
            sh 'bash /opt/upload.sh  -h http://10.0.1.19:8080 -s "Detect-secrets Scan" -f reports/detect-secrets/secrets.baseline -e 1'
            sh 'bash /opt/upload.sh  -h http://10.0.1.19:8080 -s "Dependency Check Scan" -f reports/owasp-dc/dependency-check-report.xml -e 1'
            sh 'bash /opt/upload.sh  -h http://10.0.1.19:8080 -s "ZAP Scan" -f reports/owasp-zap/owasp-zap-report.xml -e 1 || true'
        /*  sh 'bash /opt/upload.sh  -h http://10.0.1.19:8080 -s "OpenVAS CSV" -f reports/openvas/openvas-report.xml -e 1' */
      }
    }
  
    
  }
}
