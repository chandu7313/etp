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
                sh 'docker build -t etp-api:${VERSION} ./api'
                sh 'docker build -t etp-client:${VERSION} ./client'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker stop etp-api || true'
                sh 'docker rm etp-api || true'
                sh 'docker stop etp-client || true'
                sh 'docker rm etp-client || true'
                sh 'docker run -d --name etp-api -p 8080:8080 etp-api:${VERSION}'
                sh 'docker run -d --name etp-client -p 80:80 etp-client:${VERSION}'
            }
        }
    }
}
