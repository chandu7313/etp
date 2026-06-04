pipeline {
    agent any

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'The branch to build')
        string(name: 'VERSION', defaultValue: '1.0.0', description: 'The version to build')
    }

    stages {
        stage('Test API') {
            steps {
                dir('api') {
                    sh './mvnw test'
                }
            }
            post {
                always {
                    dir('api') {
                        junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml'
                    }
                }
            }
        }
        stage('Build API') {
            steps {
                dir('api') {
                    sh './mvnw clean package -DskipTests'
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
