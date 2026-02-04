pipeline {
	agent {label 'Jenkins-Agent'}
	tools {
		jdk 'Java17'
		maven 'Maven3'
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
	}
	
}
