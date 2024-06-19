pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "bhupendra1404/microservice:ms-registry"
        CONTAINER_NAME = "ms-registry"
        DOCKER_PATH = '/usr/local/bin/docker'
        MAVEN_PATH = '/opt/homebrew/Cellar/maven/3.9.5/libexec/bin/mvn'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'git@github.com:bhupendrasambare/microservices-registry.git'
            }
        }

        stage('Build JAR') {
            steps {
                sh "${MAVEN_PATH} clean package -DskipTests"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "ls"
                    // Build the Docker image, disable image pulling
                    sh "${DOCKER_PATH} build --pull=false --progress=plain -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Deploy Docker Image') {
            steps {
                script {
                    // Stop and remove the existing container if it exists
                    sh """
                    if [ \$(${DOCKER_PATH} ps -a -q -f name=${CONTAINER_NAME}) ]; then
                        ${DOCKER_PATH} stop ${CONTAINER_NAME}
                        ${DOCKER_PATH} rm ${CONTAINER_NAME}
                    fi
                    """

                    // Run the new container
                    sh """
                    ${DOCKER_PATH} run -i -p 8761:8761 -d --name ${CONTAINER_NAME} ${DOCKER_IMAGE}
                    """
                }
            }
        }
    }
}
