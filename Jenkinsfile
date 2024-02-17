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
                    dockerapp = docker.build("jamesonabade/pedelogo-catalogo:${env.BUILD_ID}", '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
                }
            }
        }

        stage('Docker Push Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage('Deploy Kubermetes') {
            agent {
                Kubermetes {
                    cloud'kubernetes'
                }
            }
            
            steps {
                kubernetesDeploy(configs: '**/k8s/**', kubeconfigId: 'kubeconfig')
            }
        }
    }
}
