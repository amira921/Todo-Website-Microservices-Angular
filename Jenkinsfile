pipeline {
    agent any
    tools {
        jdk 'Java_17'
        maven 'maven_3.6.3'
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    checkout scmGit(
                        branches: [[name: '*/master']],
                        extensions: [],
                        userRemoteConfigs: [[url: 'https://github.com/amira921/Todo-Website-Microservices-Angular']]
                    )
                }
            }
        }

        stage('Build Microservices') {
            steps {
                script {
                    def services = ['authentication-service', 'user-service', 'order-service'] // Add all your microservices here
                    services.each { service ->
                        dir(service) {
                            echo "Building ${service}..."
                            sh 'mvn clean install'
                        }
                    }
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    def services = ['authentication-service', 'user-service', 'order-service'] // Add all your microservices here
                    services.each { service ->
                        dir(service) {
                            echo "Building Docker image for ${service}..."
                            sh "docker build -t ${service.toLowerCase()} ."
                        }
                    }
                }
            }
        }

        stage('Run Services with Docker Compose') {
            steps {
                script {
                    echo "Stopping and removing old containers (if any)..."
                    sh 'docker compose down || true'

                    echo "Starting services using Docker Compose..."
                    sh 'docker compose up -d --build'
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up unused Docker resources..."
            sh 'docker system prune -f'
        }
    }
}