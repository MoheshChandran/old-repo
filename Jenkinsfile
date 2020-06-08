pipeline {
    agent any

    environment {
        K8S_CONFIG_FILE = credentials('k8s-config-file')
        ROLE = 'blue'
        DOCKER_IMAGE = "moheshchandran/hellomohesh"
    }

    stages {

	    stage('Lint HTML') {
			steps {
				sh 'tidy -q -e ./code/index.html'
			}
		}
		
        stage('Build Docker Image') {
            steps {
                script {
                    app = docker.build("$DOCKER_IMAGE", "-f ./infra/docker/$ROLE/Dockerfile .")
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

        stage('Deploy Container') {
            steps {
                withAWS(credentials: 'aws-creds', region: 'ap-southeast-1') {
                    sh """
                    kubectl apply --kubeconfig=${K8S_CONFIG_FILE} \
                        -f ./infra/k8s/deployments/${ROLE}.yaml \
                        -f ./infra/k8s/services/${ROLE}.yaml
                    """
                }
            }
        }
    }
}
