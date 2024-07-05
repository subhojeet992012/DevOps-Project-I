pipeline {
	agent { label 'Jenkins-Agent' }
	tools {
		jdk 'Java17'
		maven 'Maven3'
	}
	stages {
		stage("Cleanup Workspace"){
			steps{
			cleanWs()
			}
		}
		stage("Checkout SCM"){
			steps{
			git branch: 'raptors-branch',credentialsId: 'github',url: 'https://github.com/subhojeet992012/DevOps-Project-I'
			}
		}
		stage("Build Application"){
			steps{
			sh "mvn clean package"
			}
		}
		stage("Test Application"){
			steps{
			sh "mvn test"
			}
		}
		stage("SonarQube Analysis"){
			steps{
				script {
		        	withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') { 
                        	sh "mvn sonar:sonar"
		        	}
				}
			}
		}
		stage("Quality Gate"){
           		steps {
               			script {
                    		waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
                		}	
            		}
		}
        }
}
