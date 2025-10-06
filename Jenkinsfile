pipeline {
    agent any

    tools {
        //Maven versi 3.9
        maven 'maven-3.9'
    }

    environment {
        DOCKER_IMAGE = "fdzak01/jenkins-demo-app"
        DOCKER_TAG = "latest"
    }

    stages {

        stage('Build JAR') {
            steps {
                script {
                    echo "Building Maven package"
                    sh 'mvn clean package'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    withCredentials([usernamePassword(
                        credentialsId: 'docker-hub-credential', 
                        usernameVariable: 'USER', 
                        passwordVariable: 'PASS'
                    )]) {
                        // Build image
                        sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                        
                        // Login ke Docker Hub
                        sh 'echo $PASS | docker login -u $USER --password-stdin'
                        
                        // Push ke Docker Hub
                        sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }

        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                script {
                    echo "Deploying the application"
                    sh "docker run -d --name demo-container -p 8080:8080 ${DOCKER_IMAGE}:${DOCKER_TAG}"
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
