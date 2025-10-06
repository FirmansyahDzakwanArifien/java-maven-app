pipeline {
    agent any

    tools {
        // maven versi
        maven 'maven-3.9'
    }

    stages {

        stage('Build jar') {
            steps {
                echo "Building Maven package..."
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building the Docker image..."
                    withCredentials([usernamePassword(
                        credentialsId: 'docker-hub-credential', 
                        usernameVariable: 'USER', 
                        passwordVariable: 'PASS'
                    )]) {
                        // Build image dengan nama dan tag
                        sh 'docker build -t fdzak01/jenkins-demo-app:jma-2.0 .'

                        // Login ke Docker Hub
                        sh 'echo $PASS | docker login -u $USER --password-stdin'

                        // Push ke Docker Hub
                        sh 'docker push fdzak01/jenkins-demo-app:jma-2.0'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo "Deploying the application..."
                    sh 'docker run -d --name demo-container -p 8080:8080 fdzak01/jenkins-demo-app:jma-2.0 || true'
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline berhasil dijalankan!"
        }
        failure {
            echo "Pipeline gagal. Cek log di konsol Jenkins."
        }
    }
}
