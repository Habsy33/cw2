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
                    
                    try {
                        sh "sleep 10" // Waiting for container to start (adjust this as needed)
                        sh "docker exec $containerId ls" // Example command to test container launch
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE' // Mark the build as failed if the test fails
                        error("Test failed: ${e.message}")
                    } finally {
                        docker.stop(containerId)
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
