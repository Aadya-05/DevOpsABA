pipeline {
    // Defines the node where the pipeline will run
    agent any

    // Environment variables can be defined here if needed
    environment {
        DOCKER_IMAGE = 'devops-app'
        CONTAINER_NAME = 'devops-app'
        HOST_PORT = 8080
        CONTAINER_PORT = 5000 // Flask app default port
    }

    stages {
        // (Clone from GitHub) Stage is handled implicitly by SCM configuration

        stage('Build Docker Image') {
            steps {
                // The '.' tells Docker to look for the Dockerfile in the current directory
                bat "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Stop Old Container if any') {
            steps {
                // FIX: Use catchError to ensure the pipeline doesn't fail 
                // if the container is not found (and thus docker stop/rm fail).
                catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
                    bat "docker stop ${CONTAINER_NAME}"
                    bat "docker rm ${CONTAINER_NAME}"
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                // Run the new image in detached mode (-d), name it, and map ports.
                bat "docker run -d --name ${CONTAINER_NAME} -p ${HOST_PORT}:${CONTAINER_PORT} ${DOCKER_IMAGE}"
            }
        }
    }
}