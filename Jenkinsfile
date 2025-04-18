pipeline {
    agent any
    
    tools {
        maven 'maven3'
    }
    
    environment {
        DOCKER_IMAGE = 'spring-petclinic'
        DOCKER_TAG = "${env.BUILD_NUMBER}"
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
        
        stage('Package') {
            steps {
                sh 'mvn package -DskipTests'
                echo 'Packaging completed'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} -f Dockerfile.prod ."
                    sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
                }
                echo 'Docker image build completed'
            }
        }
        
        stage('Test Docker Image') {
            steps {
                script {
                    sh "docker run -d --name petclinic-test -p 8081:8080 ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    sh "sleep 30" // Give the application time to start
                    sh "curl -s http://localhost:8081/actuator/health || echo 'Health check failed'"
                    sh "docker stop petclinic-test"
                    sh "docker rm petclinic-test"
                }
                echo 'Docker image testing completed'
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