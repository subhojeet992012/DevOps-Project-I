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
	}
}
