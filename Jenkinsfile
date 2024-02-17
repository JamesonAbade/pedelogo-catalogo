pipeline {
    agent any //Quem vai executar ex: Imagem docker, maquina windows

    stages {
        stage('Get Source') {
            steps {
                git url:'https://github.com/JamesonAbade/pedelogo-catalogo', branch: 'main'
            }
        }
    }
    stages('Docker Build') {
        steps{
            script {
                dockerapp = docker.build("jamesonabade/pedelogo-catalogo:${env.BUILD_ID}",
                    '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
            }
        }

        stage('Docker Push Image'){
            steps{
                script{
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub')
                    dockerapp.push('latest')
                    dockerapp.push("${env.BUILD_ID}")
                }
            }
        }
    }
}