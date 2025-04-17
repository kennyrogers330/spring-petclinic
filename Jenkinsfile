pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'Checkout completed'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean compile'
                echo 'Build completed'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
                echo 'Tests completed'
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}