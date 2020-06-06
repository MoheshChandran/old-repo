pipeline {
     agent any
     stages {
		 stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
		 
		 stage('Build docker image as hellomohesh') {
            		steps {
                		script {
                    			app = docker.build("moheshchandran/hellomohesh")
                    			app.inside {
                        			sh 'echo Hello, Mohesh!'
                    			}
                		}
            		}
		}
		
		 stage('Push Docker Image') {
				steps {
					script {
							docker.withRegistry('https://registry.hub.docker.com', 'dockerlogin') {
							app.push("${env.BUILD_NUMBER}")
							app.push("latest")
							}
					}
				}
		}
		
				 stage('Deploy blue & Green container') {
				steps {
					  sshagent(['my-ssh-key']) {
						 sh "scp -o StrictHostKeyChecking=no  blue-controller.yaml green-controller.yaml blue-service.yaml ec2-user@13.251.110.131:/home/ec2-user/"
						 script{
							try{
							sh "ssh ec2-user@13.251.110.131 sudo kubectl apply -f ."
					 }catch(error){
							sh "ssh ec2-user@13.251.110.131 sudo kubectl create -f ."
									  }
						}
					 }
			   }
        }
		
		stage('Wait user approve') {
            steps {
                input "Ready to redirect traffic to green?"
            }
        }
		
		stage('Create the service in the cluster, redirect to green') {
				steps {
				  sshagent(['my-ssh-key']) {
					 sh "scp -o StrictHostKeyChecking=no  green-service.yaml ec2-user@13.251.110.131:/home/ec2-user/run/"
					 script{
						try{
						sh "ssh ec2-user@13.251.110.131 sudo kubectl apply -f ."
				 }catch(error){
						sh "ssh ec2-user@13.251.110.131 sudo kubectl create -f ."
								  }
					}
				 }
				}
		}

		
     }
}
