pipeline {
  agent any
  tools {
    maven 'maven-3.6.1'
  }
  stages {
//     stage('check git secrets') {
//       steps{
//         sh 'rm trufflehog || true'
//         sh 'docker pull gesellix/trufflehog'
//         sh 'docker run gesellix/trufflehog --json https://github.com/Siddeshwarsid/webapps-tests.git > trufflehog'
//         sh 'cat trufflehog'
//       }
//     }
//     stage('source composition analysis') {
//       steps {
//         sh 'rm owasp* || true'
//         sh ' wget "https://raw.githubusercontent.com//Siddeshwarsid/webapps-tests/main/owasp-dependency-check.sh" '
//         sh 'chmod +x owasp-dependency-check.sh'
//         sh 'bash owasp-dependency-check.sh'
//       }
//     }
    stage('test') {
      steps {
        sh 'mvn test'
        sh 'mvn pmd:pmd'
      }
      post {
        always {
          junit allowEmptyResults:true, testResults: 'target/surefire-reports/*.xml'
        }
      }
    }
    
    stage('build') {
      steps {
        sh 'mvn clean package'
      }
    }
    
    stage('Deploy to tomcat') {
      steps {
        sshagent(['tomcat']) {
          sh 'scp -o StrictHostKeyChecking=no target/*.jar ubuntu@54.90.147.139:/prod/apache-tomcat-8.5.81/webapps/webappT.jar'
        }
      }
    }
  }
}
