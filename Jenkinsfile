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
                    			app = docker.build(moheshchandran/hellomohesh)
                    			app.inside {
                        			sh 'echo Hello, Mohesh!'
                    			}
                		}
            		}
		}
		
		 stage('Push Docker Image') {
				steps {
					script {
							docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
							app.push("${env.BUILD_NUMBER}")
							app.push("latest")
							}
					}
				}
		}
		
     }
}
