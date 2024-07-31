pipeline {
    agent any 
    environment {
    DOCKERHUB_CREDENTIALS = credentials('karim-dockerhub')
    }
    stages { 

        stage('Build docker image') {
            steps {  
                sh 'docker build -t diattara/flask:$BUILD_NUMBER .'
            }
        }
        stage('Scan Docker Image') {
            steps {
                script {
                    // Run Trivy to scan the Docker image
                    def trivyOutput = sh(script: "trivy image diattara/flask:$BUILD_NUMBER", returnStdout: true).trim()

                    // Display Trivy scan results
                    println trivyOutput

                    // Check if vulnerabilities were found
                    if (trivyOutput.contains("Total: 0")) {
                        echo "No vulnerabilities found in the Docker image."
                    } else {
                        echo "Vulnerabilities found in the Docker image."
                        // You can take further actions here based on your requirements
                        // For example, failing the build if vulnerabilities are found
                        // error "Vulnerabilities found in the Docker image."
                    }
                }
            }
        }
        
        stage('login to dockerhub') {
            steps{
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('push image') {
            steps{
                sh 'docker push diattara/flask:$BUILD_NUMBER'
            }
        }
}
post {
        always {
            sh 'docker logout'
        }
    }
}
