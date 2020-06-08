pipeline {
    agent any

    environment {
        K8S_CONFIG_FILE = 'k8s-config-file'
        ROLE = 'blue'

        CI_IMAGE = "moheshchandran/hellomohesh"
    }

    stages {

        stage('Build') {
            steps {
                script {
                    app = docker.build("$CI_IMAGE", "-f ./infra/docker/$ROLE/Dockerfile .")
                }
            }
        }

        stage('Publish') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerlogin') {
                    app.push("${env.BUILD_NUMBER}")
                    app.push("latest")
                    }
                }
            }
        }

        stage('Deploy') {
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
