pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'arnolde'
        FRONTEND_IMAGE = "${DOCKER_HUB_USER}/smarttask-frontend"
        BACKEND_IMAGE  = "${DOCKER_HUB_USER}/smarttask-backend"
        TAG            = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    dockerFrontend = docker.build("${FRONTEND_IMAGE}:${TAG}", "./frontend")
                    dockerBackend  = docker.build("${BACKEND_IMAGE}:${TAG}", "./backend")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials-id') {
                        dockerFrontend.push("${TAG}")
                        dockerFrontend.push("latest")
                        dockerBackend.push("${TAG}")
                        dockerBackend.push("latest")
                    }
                }
            }
        }
    }

    post {
        failure {
            echo "Le Pipeline CI/CD a échoué."
        }
        success {
            echo "Images construites et poussées sur Docker Hub avec succès."
        }
    }
}
