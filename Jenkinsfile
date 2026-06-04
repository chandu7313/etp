pipeline {
    agent any

    stages {
        stage('Test API') {
            steps {
                dir('api') {
                    sh 'mvn test'
                }
            }
            post {
                always {
                    dir('api') {
                        junit 'target/surefire-reports/*.xml'
                    }
                }
            }
        }
        stage('Build API') {
            steps {
                dir('api') {
                    sh 'mvn clean package -DskipTests'
                }
            }
            post {
                success {
                    dir('api') {
                        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                    }
                }
            }
        }
        stage('Build Docker Images') {
            steps {
                sh 'docker-compose build'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker-compose up -d'
            }
        }
    }
}
