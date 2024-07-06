pipeline {
	agent { label 'Jenkins-Agent' }
	tools {
		jdk 'Java17'
		maven 'Maven3'
		dockerTool 'docker-install'
	}
	environment {
	    APP_NAME = "register-app-pipeline"
            RELEASE = "1.0.0"
            DOCKER_USER = "subhojeet992012"
            DOCKER_PASS = 'jenkins-docker-token'
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
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
		stage("Build & Push Docker Image") {
    			steps {
        			script {
            			// Build Docker image
            			sh "docker build -t ${IMAGE_NAME} ."

           			 // Log in to Docker registry and push images
				 withCredentials([usernamePassword(credentialsId: 'jenkins-docker-token', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        	 sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                    		 }	
            			sh "docker tag ${IMAGE_NAME}:latest ${IMAGE_NAME}:${IMAGE_TAG}"
            			sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
            			sh "docker push ${IMAGE_NAME}:latest"
        			}
    			}
		}
		stage("Trivy Scan") {
           		steps {
               			script {
	            		sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image subhojeet992012/register-app-pipeline:latest --scanners vuln  --exit-code 0 --format table')
               			}
           		}
       		}
        }
}
