pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "ahmed23a/nginxtest"
        TAG = "tagname"
        BUILD_TAG = "${DOCKER_IMAGE}:${BUILD_NUMBER}"
        K8S_SERVER = "ubuntu@172.31.63.167" // Replace with your Kubernetes server address
        SSH_CREDENTIAL_ID = "cb1a3b84-ccee-4852-9185-64c5082d3c01" // Your SSH credential ID
    }

    stages {
        stage('Build Docker image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${TAG} ."
                    sh "docker tag ${DOCKER_IMAGE}:${TAG} ${BUILD_TAG}"
                }
            }
        }

        stage('Test container can be launched') {
            steps {
                script {
                    def runningContainers = sh(script: "docker ps -q", returnStdout: true).trim()
                    if (runningContainers) {
                        sh 'docker rm -f $(docker ps -aq)'
                        sh 'sleep 10'
                    }
                    def containerName = "cw02-app"
                    sh "docker run -d --name ${containerName} -p 8000:8000 ${DOCKER_IMAGE}:${TAG}"
                    sh "echo 'Container launched successfully!'"
                    sh "docker stop ${containerName}"
                    sh "docker rm ${containerName}"
                    sh 'sleep 10'
                }
            }
        }

        stage('Push image to Docker Hub') {
            steps {
                withDockerRegistry([ credentialsId: "298723b5-5574-45b3-aae7-d23db1348d60", url: "" ]) {
                    sh "docker push ${DOCKER_IMAGE}:${TAG}"
                    sh "docker push ${BUILD_TAG}" 
                }
            }
        }
