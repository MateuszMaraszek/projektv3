pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'guess-my-number'
        DOCKER_TAG = 'latest'
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

        stage('Docker Build') {
            steps {
                bat "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage('Done') {
            steps {
                echo 'Build zakończony.'
            }
        }
    }

    post {
        success {
            emailext(
                subject: "✅ Build Success: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build zakończony sukcesem.\n${env.BUILD_URL}",
                to: "m.maraszek.081@studms.ug.edu.pl"
            )
        }

        failure {
            emailext(
                subject: "❌ Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build zakończony błędem.\n${env.BUILD_URL}",
                to: "m.maraszek.081@studms.ug.edu.pl"
            )
        }
    }
}
