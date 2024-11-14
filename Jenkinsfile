pipeline {
    agent any
    
    environment {
        IMAGE_NAME = "my-app"            // Change this to your desired Docker image name
        IMAGE_TAG = "latest"
    }
    
    
        
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'   // Packages the application with Maven
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'  // Builds Docker image
            }
        }
        
        stage('Run Docker Container') {
            steps {
                script {
                    // Run Docker container and get the port it's running on
                    def containerId = sh(
                        script: "docker run -d -p 8080:8080 ${IMAGE_NAME}:${IMAGE_TAG}", 
                        returnStdout: true
                    ).trim()
                    
                    // Get the exposed port
                    def runningPort = sh(
                        script: "docker inspect -f '{{ (index (index .NetworkSettings.Ports \"8080/tcp\") 0).HostPort }}' ${containerId}", 
                        returnStdout: true
                    ).trim()
                    
                    echo "Docker container is running on port: ${runningPort}"
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()   // Clean workspace after build
            sh 'docker stop $(docker ps -q --filter ancestor=${IMAGE_NAME}:${IMAGE_TAG}) || true'  // Stop container if running
            sh 'docker rm $(docker ps -a -q --filter ancestor=${IMAGE_NAME}:${IMAGE_TAG}) || true'  // Remove container if exists
        }
    }
}
