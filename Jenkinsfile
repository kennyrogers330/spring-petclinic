pipeline {
    agent any
    
    tools {
        maven 'maven3'
    }
    
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
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    mvn sonar:sonar \
                      -Dsonar.projectKey=spring-petclinic \
                      -Dsonar.projectName='Spring Pet Clinic' \
                      -Dsonar.java.binaries=target/classes
                    '''
                }
                echo 'SonarQube analysis completed'
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
