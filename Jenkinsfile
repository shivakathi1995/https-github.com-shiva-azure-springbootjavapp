pipeline {
      agent any
      tools {
        maven 'maven'
      }   
      environment {
        TENANT_ID       = '596f271a-e744-4410-9203-1836891565e6'
        ACR_NAME        = 'acrregistry2302'
        IMAGE_NAME      = 'springbootjavapp'
        IMAGE_TAG       = "${BUILD_NUMBER}"
        FULL_IMAGE_NAME = "${ACR_NAME}.azurecr.io/${IMAGE_NAME}:${IMAGE_TAG}"
    }
      }
      stages {
        stage('check out from Git') {
            steps{
            git branch: 'prod', url: 'https://github.com/shivakathi1995/https-github.com-shiva-azure-springbootjavapp.git'
            }
        }
      // stage ('maven validate') {
      //     steps {
      //       sh 'mvn validate'
      //     }
      // }
      // stage ('maven compile') {
      //     steps {
      //       sh 'mvn compile'
      //     }
      // }
      // stage ('maven test') {
      //     steps {
      //       sh 'mvn test'
      //     }
      // }
      // stage ('maven install') {
      //     steps {
      //       sh 'mvn install'
      //     }
      // }
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
                -Dsonar.java.binaries=target/classes
              """
            }
           }
        }
        stage ('maven package') {
            steps {
              sh 'mvn package'
            }
        }
       stage('Sonar Quality Gate') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar'
                    echo "Sonar Quality Gate Finished"
                }
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    echo "Build Docker Image"
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
          stage('Docker Push')
        {
            steps
            {
                script {
                    echo "Docker Image Push"
                    sh '''
                        docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${FULL_IMAGE_NAME}
                        docker push ${FULL_IMAGE_NAME}
                    '''
                }
            }
        }
        }
      }
}
