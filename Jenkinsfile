pipeline {
    agent any
    
    tools {
        maven 'csemaven'
        jdk 'JDK-21'
    }
    
<<<<<<< HEAD
=======
    environment {
        DOCKER_IMAGE = 'laharsri/edupulse'
        SONAR_HOST = 'http://localhost:9000'
    }
    
>>>>>>> b82babb87fbaa080fb087a82b9ec5f1083ae877b
    stages {
        stage('Checkout') {
            steps {
                // Pulls the latest code from your GitHub repository
                checkout scm
                echo ' Code checked out from GitHub'
            }
        }
        
        stage('Build') {
            steps {
                // Build the application and skip tests
                bat 'mvn clean package -DskipTests'
                echo ' Build completed successfully'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat 'mvn sonar:sonar -Dsonar.projectKey=edupulse -Dsonar.host.url=http://localhost:9000'
                }
                echo ' SonarQube analysis completed'
            }
        }
        
        stage('Archive Artifacts') {
            steps {
                // Saves the JAR file inside Jenkins
                archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true
                echo ' JAR file archived'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t edupulse:latest .'
                bat 'docker tag edupulse:latest laharisri/edupulse:latest'
                echo ' Docker image built and tagged'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                bat 'docker push laharisri/edupulse:latest'
                echo ' Docker image pushed to Docker Hub'
            }
        }
    }
    
    post {
        success {
            echo ' Pipeline completed successfully!'
            echo ' Docker Image: laharisri/edupulse:latest'
            echo ' Docker Hub: https://hub.docker.com/r/laharisri/edupulse'
            echo 'Local URL: http://localhost:8087'
        }
        failure {
            echo ' Pipeline failed! Check the Console Output for details.'
        }
    }
}
