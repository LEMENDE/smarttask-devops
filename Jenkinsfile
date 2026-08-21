pipeline {
    agent any

    environment {
        PATH = "/usr/bin:/usr/local/bin:${env.PATH}"
        DOCKER_HUB_USER = 'lemende'
        FRONTEND_IMAGE  = "${DOCKER_HUB_USER}/smarttask-frontend"
        BACKEND_IMAGE   = "${DOCKER_HUB_USER}/smarttask-backend"
        TAG             = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Images') {
            steps {
                sh "docker build -t ${FRONTEND_IMAGE}:${TAG} -t ${FRONTEND_IMAGE}:latest ./frontend"
                sh "docker build -t ${BACKEND_IMAGE}:${TAG} -t ${BACKEND_IMAGE}:latest ./backend"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials-id', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo \$PASS | docker login -u \$USER --password-stdin"
                    sh "docker push ${FRONTEND_IMAGE}:${TAG}"
                    sh "docker push ${FRONTEND_IMAGE}:latest"
                    sh "docker push ${BACKEND_IMAGE}:${TAG}"
                    sh "docker push ${BACKEND_IMAGE}:latest"
                }
            }
        }

        stage('Deploy Dev Environment') {
            steps {
                sh "docker compose down || true"
                sh "docker compose pull || true"
                sh "docker compose up -d"
            }
        }
    }

    post {
        failure {
            echo "Le Pipeline CI/CD a échoué."
        }
        success {
            echo "Images construites, poussées et déployées avec succès !"
        }
    }
}
