pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'guess-my-number'
        DOCKER_TAG = 'latest'
        REGISTRY = 'docker.io/mateuszmaraszek'
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
        failure {
            script {
                echo '🔄 Rozpoczynanie automatycznego rollback...'
                try {
                    bat """
                        echo "Zatrzymywanie nowej wersji aplikacji..."
                        docker stop guess-my-number-app || echo "Kontener nie istnieje"
                        docker rm guess-my-number-app || echo "Kontener nie istnieje"
                        
                        echo "Uruchamianie poprzedniej wersji z backup..."
                        docker run -d --name guess-my-number-app -p 8080:80 ${REGISTRY}/${DOCKER_IMAGE}:backup || docker run -d --name guess-my-number-app -p 8080:80 nginx:alpine
                    """
                    echo '✅ Rollback zakończony pomyślnie'
                } catch (Exception e) {
                    echo "❌ Rollback nie powiódł się: ${e.getMessage()}"
                }
            }
            
            emailext(
                subject: "❌ Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build/deploy zakończony błędem. Wykonano automatyczny rollback.\n${env.BUILD_URL}",
                to: "mateuszmaraszek77@gmail.com"
            )
        }
        success {
            emailext(
                subject: "✅ Build Success: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build i deploy zakończone sukcesem. :) \n${env.BUILD_URL}",
                to: "mateuszmaraszek77@gmail.com"
            )
        }
    }
}
