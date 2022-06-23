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
                sh 'mkdir -p reports/detect-secrets'
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
          sh 'mkdir -p reports/owasp-zap'
          sh 'docker run -v $(pwd)/reports/owasp-zap/:/zap/wrk/:rw --user root -t owasp/zap2docker-stable zap-baseline.py -t http://192.168.52.131:8088/webapp/ -x owasp-zap-report || true'
      }
    }
    
    
    stage ('Uplaod reports to DefectDojo') {
      steps {
            sh 'bash /opt/upload.sh  -h http://127.0.0.1:8080 -s "Detect-secrets Scan" -f reports/detect-secrets/secrets.baseline -e 27'
            sh 'bash /opt/upload.sh  -h http://127.0.0.1:8080 -s "Dependency Check Scan" -f reports/owasp-dc/dependency-check-report.xml -e 27'
            sh 'bash /opt/upload.sh  -h http://127.0.0.1:8080 -s "ZAP Scan" -f reports/owasp-zap/owasp-zap-report.xml -e 27 || true'
      }
    }
  
    
  }
}
