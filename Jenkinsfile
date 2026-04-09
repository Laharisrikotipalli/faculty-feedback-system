pipeline {
    agent any
    
    tools {
        maven 'csemaven'
        jdk 'JDK-21'
    }
    
    environment {
        DOCKER_IMAGE = 'yourusername/edupulse'
        SONAR_HOST = 'http://localhost:9000'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                bat 'mvn clean package -DskipTests'
            }
        }
        
        stage('Test') {
            steps {
                bat 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('SonarQube') {
            steps {
                bat 'mvn sonar:sonar'
            }
        }
        
        stage('Docker Build') {
            steps {
                bat 'docker build -t ${DOCKER_IMAGE}:latest .'
            }
        }
        
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat '''
                        echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                        docker push ${DOCKER_IMAGE}:latest
                    '''
                }
            }
        }
        
        stage('Deploy') {
            steps {
                bat 'kubectl apply -f deployment.yaml'
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}