pipeline {
    agent any
    
    tools {
        jdk 'java-11'
        maven 'maven'
    }
    
    environment {
        DOCKER_USERNAME = 'nikhil1356'
        IMAGE_NAME = 'nikhil1356/project-1'
        CONTAINER_NAME = 'project-1-container'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                echo "Cloning the code..."
                git branch: 'dev', url: 'https://github.com/manjukolkar/web-application.git'
            }
        }
        
        stage('Code Compile') {
            steps {
                echo "Compiling code..."
                sh 'mvn compile'
            }
        }
        
        stage('Code Package') {
            steps {
                echo "Packaging code..."
                sh 'mvn clean install'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${IMAGE_NAME}"
                sh 'docker build -t ${IMAGE_NAME} .'
            }
        }
        
        stage('Login to Docker Hub') {
            steps {
                echo "Logging in to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin'
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                echo "Pushing image to Docker Hub: ${IMAGE_NAME}"
                sh 'docker push ${IMAGE_NAME}'
            }
        }
        
        stage('Run Container') {
            steps {
                echo "Running container..."
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true
                    docker run -it -d \
                      --name ${CONTAINER_NAME} \
                      -p 9008:8080 \
                      ${IMAGE_NAME}
                '''
            }
        }
    }
    
    post {
        success {
            echo "✓ Pipeline completed successfully!"
            echo "✓ Image pushed to: ${IMAGE_NAME}"
            echo "✓ Container running on port 9008"
        }
        failure {
            echo "✗ Pipeline failed! Check logs above."
        }
    }
}
