pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'habsy33/dockerfile:2.0'
    }

    stages {
        stage('Build') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE, '-f Dockerfile .')
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerHubCredentials') {
                        docker.image(DOCKER_IMAGE).push()
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded! Image built and pushed to DockerHub.'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
