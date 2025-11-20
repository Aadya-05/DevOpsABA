pipeline {
    // Defines the node where the pipeline will run
    agent any

    // Environment variables can be defined here if needed
    environment {
        DOCKER_IMAGE = 'devops-app'
        CONTAINER_NAME = 'devops-app'
        HOST_PORT = 8080
        CONTAINER_PORT = 5000 // Flask app default port
        
        // FIX 1: Path to the directory containing Dockerfile, app.py, and requirements.txt
        // This path is relative to the root of your Git repository checkout.
        APP_DIR = 'aadya-05/devopsaba/DevOpsABA-dfdc9db56b488281ee9642879c8464210deec6be' 
    }

    stages {
        // (Clone from GitHub) Stage is handled implicitly by SCM configuration

        stage('Build Docker Image') {
            steps {
                // FIX 2: Use the 'dir' step to execute docker build inside the correct subdirectory
                dir("${APP_DIR}") { 
                    // The '.' tells Docker to look for the Dockerfile in the current directory (which is now APP_DIR)
                    bat "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Stop Old Container if any') {
            steps {
                // FIX 3: Use catchError to prevent pipeline failure if the container is not found.
                // This is required because '|| true' does not work reliably in a Windows 'bat' step.
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
