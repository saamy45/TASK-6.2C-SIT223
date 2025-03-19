pipeline {
    agent any

    environment {
        EMAIL_RECIPIENTS = '45iamrohit@gmail.com'
    }

    stages {
        stage('Build') {
            steps {
                bat 'echo Building project...'
            }
        }

        stage('Unit and Integration Tests') {
            steps {
                echo 'Running the unit and integrating the tests...'
                sh 'mvn test'
            }
            post {
                always {
                    script {
                        emailext subject: "Jenkins Build: Unit and Integration Tests",
                            body: "Unit and Integration Tests stage completed. Check the logs for details.",
                            to: "${EMAIL_RECIPIENTS}",
                            attachLog: true
                    }
                }
            }
        }

        stage('Code Analysis') {
            steps {
                echo 'Code analysis has been started'
                sh 'mvn sonar:sonar'
            }
        }

         stage('Security Scan') {
            steps {
                echo 'Performing the Security scan...'
                sh 'trivy fs .'
            }
            post {
                always {
                    script {
                        emailext subject: "Jenkins Build: Security Scan Completed",
                            body: "Security Scan stage completed. Check the logs for any kind of vulnerabilities.",
                            to: "${EMAIL_RECIPIENTS}",
                            attachLog: true
                    }
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo 'Deploying the code staging server...'
                sh 'scp target/*.jar user@staging-server:/app'
            }
        }

        stage('Integration Tests on Staging') {
            steps {
                echo 'Integration of tests on staging has been started...'
                sh 'curl -X GET http://staging-server/health'
            }
        }

        stage('Deploy to Production') {
            steps {
                echo 'Deploying the code to production server...'
                sh 'scp target/*.jar user@production-server:/app'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline Unsuccesful!'
        }
    }
}
