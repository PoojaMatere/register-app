pipeline {
	agent {label 'Jenkins-Agent'}
	tools {
		jdk 'Java17'
		maven 'Maven3'
	}
	environment{
		APP_NAME="register-app-pipeline"
	    RELEASE="1.0.0"
		DOCKER_USER="pooja7313"
		DOCKER_PASS = credentials('dockerhub')
        IMAGE_NAME="${DOCKER_USER}" + "/" + "${APP_NAME}"
		IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"

	}
	stages{
		stage("Cleanup Workspace")
		{
			steps{
				cleanWs()
			}
		}
		stage("Checkout from SCM"){
			steps{
				git branch: 'main',credentialsId:'github' ,url: 'https://github.com/PoojaMatere/register-app.git'
			}
		}
		stage ("Build Application"){
			steps{
				sh "mvn clean package"
			}
		}
		stage ("Test Applciation"){
			steps{
				sh "mvn test"
			}
		}
		stage ("Sonarqube Analysis"){
			steps{
				script{
					withSonarQubeEnv(CredentialsId: 'jenkins-sonarquebe-token'){
						sh "mvn sonar:sonar"
					}
				}
			}
		}
		stage ("Quality gate"){
			steps {
				script {
					waitForQualityGate abortPipeline: false 
				}
			}
		}
		stage ("Build and Push Docker Image"){
			steps {
				script {
					   docker.withRegistry('https://index.docker.io/v1/','dockerhub'){
						def docker_image = docker.build "${IMAGE_NAME}"
						docker_image.push("${IMAGE_TAG}")
						docker_image.push('latest')
					   }
					
					   
				}
			}
		}
		stage("Trivy Scan") {
           steps {
               script {
	            sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image pooja7313/register-app-pipeline:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
               }
           }
       }

       stage ('Cleanup Artifacts') {
           steps {
               script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
               }
          }
       }
	}
	
}
