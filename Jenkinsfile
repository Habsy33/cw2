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

        stage('Test') {
            steps {
                script {
                    def containerId = docker.run("-p 8080:8080 -d ${DOCKER_IMAGE}")
                    sh "docker exec $containerId ls" // Example command to test container launch
                    docker.stop(containerId)
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
            echo 'Pipeline succeeded! Image built, tested, and pushed to DockerHub.'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
