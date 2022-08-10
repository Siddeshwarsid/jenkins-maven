pipeline{
  agent any
  tools {
    maven 'Maven'
  }
   stages {
    stage('Initialize') {
      steps {
        sh '''
            echo "PATH = ${PATH}"
            echo "M2_HOME = ${M2_HOME}"
           '''
     }
   }
    stage ('owasp') {
      steps {
        sh 'rm owasp* || true'
        sh 'wget "https://raw.githubusercontent.com/Siddeshwarsid/webapp/master/owasp-dependency-check.sh" '
        sh 'chmod +x owasp-dependency-check.sh'
        sh 'bash owasp-dependency-check.sh'
        sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
      }
    }
     stage('Unit Test'){
       steps {
         sh 'mvn test'
        }
        post {
          always {
            junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml'   
         }
       } 
     }
     stage('INTEGRATION TEST'){
       steps {
         sh 'mvn verify -DskipUnitTests'
        }
      }
      stage ('CODE ANALYSIS WITH CHECKSTYLE'){
        steps {
          sh 'mvn checkstyle:checkstyle'
          }
          post {
            success {
               echo 'Generated Analysis Result'
              }
           }
        }
        stage('sonarQube') {
          steps { 
             withSonarQubeEnv('sonar') {
                sh 'mvn sonar:sonar'
                sh 'cat target/sonar/report-task.txt'
               }  
             }
           }
         stage("Quality Gate") {
           steps {
             timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true, credentialsId: 'sonar-1'
              }
            }
          }
          stage('build') {
             steps {
               sh 'mvn clean package'
               }
             }
             stage('Arching') {
               steps {
                 archiveArtifacts '**/target/*.jar' 
              } 
          }
    }
}
     
