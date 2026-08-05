pipeliene {
      agent any
      tools {
        maven 'Maven 3.9.16'
      }   
      environment {
        TENANT_ID = '596f271a-e744-4410-9203-1836891565e6'
      }
      stages {
        stage ('check out from Git') 
        {
          steps {
            git branch: prod, url: 'https://github.com/shiva-azure/springboot-java-app.git'
          }
        }
      }
      stage{
          steps {
            sh 'mvn validate'
          }
      }
      stage{
          steps {
            sh 'mvn compile'
          }
      }












}