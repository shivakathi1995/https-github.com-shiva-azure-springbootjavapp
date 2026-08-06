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

    stages {
        stage('check out from Git') {
            steps {
                git branch: 'prod', url: 'https://github.com/shivakathi1995/https-github.com-shiva-azure-springbootjavapp.git'
            }
        }

        stage('Trivy scan') {
            steps {
                echo 'Trivy scan started' 
                sh 'trivy fs --format table --output trivy-report.txt --severity HIGH,CRITICAL .'
                echo 'Trivy scan finished'
            }
        }

        stage('maven package') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Sonar Analysis') {
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
        }

        stage('Azure Login and Push to ACR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azure-acr-spn', usernameVariable: 'AZURE_USERNAME', passwordVariable: 'AZURE_PASSWORD')]) {
                    script {
                        echo "Logging into Azure & Pushing Image"
                        sh """
                            az login --service-principal -u \$AZURE_USERNAME -p \$AZURE_PASSWORD --tenant ${TENANT_ID}
                            az acr login --name ${ACR_NAME}
                            docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${FULL_IMAGE_NAME}
                            docker push ${FULL_IMAGE_NAME}
                        """
                    }
                }
            }
        }
    }
}