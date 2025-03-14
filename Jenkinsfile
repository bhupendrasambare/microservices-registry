pipeline {
    agent any

    environment {
        JAVA_HOME = "/Users/bhupendrasam1404/Library/Java/JavaVirtualMachines/jdk-21.0.6.jdk/Contents/Home"
        DOCKER_IMAGE = "bhupendra1404/microservice:ms-registry"
        CONTAINER_NAME = "ms-registry"
        DOCKER_PATH = '/usr/local/bin/docker'
        MAVEN_PATH = '/opt/homebrew/Cellar/maven/3.9.9/libexec/bin/mvn'
        CUSTOM_SERVER_IP = '192.168.29.226'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/bhupendrasambare/microservices-registry.git'
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
                    // Build the Docker image using the standard build command
                    sh "${DOCKER_PATH} build --no-cache -t ${DOCKER_IMAGE} ."
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
                    ${DOCKER_PATH} run -i -p 8761:8761 -d -e CUSTOM_SERVER_IP=${CUSTOM_SERVER_IP} --name ${CONTAINER_NAME} ${DOCKER_IMAGE}
                    """
                }
            }
        }
    }
}
