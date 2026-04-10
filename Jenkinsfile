pipeline {
    agent any
    
    tools {
        maven 'csemaven'
        jdk 'JDK-21'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'Code checked out'
            }
        }
        
        stage('Build') {
            steps {
                bat 'mvn clean package -DskipTests'
                echo 'Build completed'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat 'mvn sonar:sonar'
                }
                echo 'SonarQube analysis completed'
            }
        }
        
        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true
                echo 'JAR file archived'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t edupulse:latest .'
                bat 'docker tag edupulse:latest laharisri/edupulse:latest'
                echo 'Docker image built'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                bat 'docker push laharisri/edupulse:latest'
                echo 'Docker image pushed'
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully'
            echo 'Docker Image: laharisri/edupulse:latest'
        }
        failure {
            echo 'Pipeline failed. Check the Console Output for details.'
        }
    }
}
