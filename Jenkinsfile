pipeline {
    agent any
    
    tools {
        jdk 'java-11'
        maven 'maven'
    }
    
    environment {
        IMAGE_NAME = 'nikhil123/project-1'
        CONTAINER_NAME = 'project-1-container'
        REGISTRY = 'docker.io'
        DOCKER_CREDENTIALS = 'docker-hub-credentials-nikhil123'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                echo "========== Cloning Repository =========="
                git branch: 'master',
                    url: 'https://github.com/SUNIKHIL/web-application.git'
                echo "✓ Code cloned successfully"
            }
        }
        
        stage('Code Compile') {
            steps {
                echo "========== Compiling Code =========="
                sh 'mvn compile'
                echo "✓ Code compiled successfully"
            }
        }
        
        stage('Code Package') {
            steps {
                echo "========== Packaging Application =========="
                sh 'mvn clean package -DskipTests'
                echo "✓ Application packaged successfully"
            }
        }
        
        stage('Docker Build') {
            steps {
                echo "========== Building Docker Image =========="
                echo "Image name: ${IMAGE_NAME}"
                sh 'docker build -t ${IMAGE_NAME}:latest .'
                sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .'
                echo "✓ Docker image built successfully"
            }
        }
        
        stage('Docker Login') {
            steps {
                echo "========== Authenticating to Docker Hub =========="
                withCredentials([
                    usernamePassword(
                        credentialsId: "${DOCKER_CREDENTIALS}",
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh 'echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin'
                    echo "✓ Docker Hub login successful"
                }
            }
        }
        
        stage('Docker Push') {
            steps {
                echo "========== Pushing Image to Docker Hub =========="
                sh 'docker push ${IMAGE_NAME}:latest'
                sh 'docker push ${IMAGE_NAME}:${BUILD_NUMBER}'
                echo "✓ Image pushed successfully to Docker Hub"
            }
        }
        
        stage('Run Container') {
            steps {
                echo "========== Starting Docker Container =========="
                sh '''
                    # Remove existing container if running
                    docker rm -f ${CONTAINER_NAME} || true
                    
                    # Run the new container
                    docker run -d \
                      --name ${CONTAINER_NAME} \
                      -p 9008:8080 \
                      ${IMAGE_NAME}:latest
                    
                    # Show container status
                    docker ps | grep ${CONTAINER_NAME}
                '''
                echo "✓ Container started successfully on port 9008"
            }
        }
    }
    
    post {
        success {
            echo "========== PIPELINE SUCCESSFUL =========="
            echo "✓ Application built and deployed successfully"
            echo "✓ Docker image: ${IMAGE_NAME}:latest"
            echo "✓ Container name: ${CONTAINER_NAME}"
            echo "✓ Access at: http://localhost:9008"
        }
        failure {
            echo "========== PIPELINE FAILED =========="
            echo "✗ Check the logs above for details"
        }
        always {
            echo "========== Cleaning up workspace =========="
            sh 'docker logout || true'
        }
    }
}
