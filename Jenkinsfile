pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'habsy33/dockerfile:2.0'
        DOCKER_BIN = '/usr/bin/docker' // Replace with the correct path to the docker binary in your system
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
                    // Start the container with a different port mapping
                    sh "${DOCKER_BIN} run -p 8090:8080 -d --name my_container ${DOCKER_IMAGE}"
                    
                    // Check if the container is running
                    def containerStatus = sh(script: "${DOCKER_BIN} inspect -f {{.State.Running}} my_container", returnStdout: true).trim()
                    
                    if (containerStatus == 'true') {
                        echo "Container is running"
                    } else {
                        error "Container failed to start"
                    }
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
