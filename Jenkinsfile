pipeline {
    
  agent any
  
   environment {
    imageName = "docker_image"
	registryCredentials = "nexus"
	registry = "ec2-54-146-220-123.compute-1.amazonaws.com:8085/"
	dockerImage = ""
  }
  
   stages {
       
        stage('Cloning Github project/source code') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/yogesh2188/Nexus-Image.git']])
            }
        }
        
        stage('Building the docker image') {
            steps{
                 script {
                   dockerImage = docker.build imageName
               }
            }
        }
		
		stage('Upload into Nexus'){
		      steps{
			         script{
				             docker.withRegistry('http://'+registry, registryCredentials){
			                 dockerImage.push('latest')
			            	}
				   }	
			}
		}
		
		// Stopping Docker containers for cleaner Docker run
    stage('stop previous containers') {
         steps {
            sh 'docker ps -f name=myjavacontainer -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=myjavacontainer -q | xargs -r docker container rm'
         }
       }
      
    stage('Docker Run') {
       steps{
         script {
                sh 'docker run -d -p 8085:8080 --rm --name myjavacontainer ' + registry + imageName
            }
         }
      }    
   }
}
