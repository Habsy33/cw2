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
                    // Start the container
                    sh "docker run -p 8080:8080 -d ${DOCKER_IMAGE}"
                    
                    // Wait for the container to start (adjust sleep duration as needed)
                    sh "sleep 10"
                    
                    // Example command to test container launch
                    sh 'docker exec $(docker ps -q) ls'
                    
                    // Stop and remove the container
                    sh 'docker stop $(docker ps -q)'
                    sh 'docker rm $(docker ps -aq)'
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
