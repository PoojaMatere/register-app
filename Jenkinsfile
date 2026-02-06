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
		DOCKER_PASS='dockerhub'
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
					withSonarQubeEnv(CredentialsId: 'Jenkins-sonarqube-token'){
						sh "mvn sonar:sonar"
					}
				}
			}
		}
		stage ("Quality gate"){
			steps {
				script {
					waitForQualityGate abortPipeline: false , CredentialsId: 'Jenkins-sonarqube-token'
				}
			}
		}
		stage ("Build and Push Docker Image"){
			steps {
				script {
					   docker.withRegistry('https://index.docker.io/v1/',DOCKER_PASS){
						docker_image = docker.build "${IMAGE_NAME}"
					   }
					   docker.withRegistry('https://index.docker.io/v1/',DOCKER_PASS){
						   docker_image.push("${IMAGE_TAG}")
						   docker_image.push('latest')
					   }
					
					   
				}
			}
		}
	}
	
}
