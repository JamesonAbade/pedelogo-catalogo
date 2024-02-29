pipeline {
    agent any // Quem vai executar, por exemplo, imagem docker ou máquina Windows

    stages {
        stage('Get Source') {
            steps {
                git url:'https://github.com/JamesonAbade/pedelogo-catalogo', branch: 'main'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    dockerapp = docker.build("registry-uesb:5000/pedelogo-catalogo:${env.BUILD_ID}", '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
                }
            }
        }

        stage('Docker Push Image') {
            steps {
                script {
                    docker.withRegistry('registry-uesb:5000', 'resgistry-uesb') {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage('Deploy Kubernetes') {
            agent {
                kubernetes {
                    cloud 'kubeuesb'
                }
            }
            environment {
                tag_version = "${env.BUILD_ID}"
            }

            steps {
                sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/api.yaml'
                sh 'cat ./k8s/api.yaml'
                kubernetesDeploy(configs: '**/k8s/**', kubeconfigId: 'kubeconfig')
            }
        }
    }
}
