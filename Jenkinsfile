pipeline {
      agent any
      tools {
        maven 'maven'
      }   
      environment {
        TENANT_ID = '596f271a-e744-4410-9203-1836891565e6'
      }
      stages {
        stage('check out from Git') {
            steps{
            git branch: 'prod', url: 'https://github.com/shivakathi1995/https-github.com-shiva-azure-springbootjavapp.git'
            }
        }
      stage ('maven validate') {
          steps {
            sh 'mvn validate'
          }
      }
      stage ('maven compile') {
          steps {
            sh 'mvn compile'
          }
      }
      stage ('maven test') {
          steps {
            sh 'mvn test'
          }
      }
      stage ('maven install') {
          steps {
            sh 'mvn install'
          }
      }
      stage ('Trivy scan') {
          steps {
            echo 'Trivy scan started' 
            sh 'trivy fs --format table --output trivy-report.txt --severity HIGH,CRITICAL .'
            echo 'Trivy scan finished'
           }
        }
      stage ('Sonar Analysis') {
          environment {
            SCANNER_HOME = tool 'sonar-scanner'
          }
          steps {
            echo 'SonarQube scan started' 
            withSonarQubeEnv('sonar-server') {
              sh """ 
                ${SCANNER_HOME}/bin/sonar-scanner \
                -Dsonar.organization=shiva2302 \
                -Dsonar.projectKey=shivakathi1995_https-github.com-shiva-azure-springbootjavapp \
                -Dsonar.projectName=https-github.com-shiva-azure-springbootjavapp \
                -Dsonar.java.binaries=.
              """
            }
           }
        }
    
    
    }
}