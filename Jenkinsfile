pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'guess-my-number'
        DOCKER_TAG = 'latest'
        REGISTRY = 'docker.io/mateuszmaraszek77'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Static Check') {
            steps {
                echo 'Sprawdzanie plików HTML/CSS/JS...'
                bat 'dir index.html style.css script.js'
            }
        }

        stage('Unit Tests') {
            steps {
                echo 'Uruchamianie testów jednostkowych...'
                bat 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage('Push to Registry') {
            steps {
                echo 'Wysyłanie obrazu do rejestru...'
                bat "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}"
                bat "docker push ${REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploy aplikacji...'
            }
        }

        stage('Integration Tests') {
            steps {
                echo 'Testy integracyjne...'
            }
        }
    }

    post {
        success {
            emailext(
                subject: "✅ Build Success: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build i deploy zakończone sukcesem. :) \n${env.BUILD_URL}",
                to: "mateuszmaraszek77@gmail.com"
            )
        }

        failure {
            emailext(
                subject: "❌ Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build/deploy zakończony błędem.\n${env.BUILD_URL}",
                to: "mateuszmaraszek77@gmail.com"
            )
        }
    }
}
