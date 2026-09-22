
pipeline {
    agent any

    environment {
        PATH = "/opt/homebrew/bin:${env.PATH}"
        NOTIFY_EMAIL = 'utkrist16@gmail.com'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Utkrist16/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test 2>&1 | tee test-log.txt || true'
            }
            post {
                always {
                    script {
                        env.TEST_STATUS = currentBuild.currentResult
                    }
                    emailext(
                        to: "${env.NOTIFY_EMAIL}",
                        subject: "Jenkins - Run Tests Stage [${env.TEST_STATUS}] - Build #${env.BUILD_NUMBER}",
                        body: "The 'Run Tests' stage finished with status: ${env.TEST_STATUS}.\n\nSee attached log for details.",
                        attachmentsPattern: 'test-log.txt'
                    )
                }
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh 'npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit | tee audit-log.txt || true'
            }
            post {
                always {
                    script {
                        env.AUDIT_STATUS = currentBuild.currentResult
                    }
                    emailext(
                        to: "${env.NOTIFY_EMAIL}",
                        subject: "Jenkins - Security Scan Stage [${env.AUDIT_STATUS}] - Build #${env.BUILD_NUMBER}",
                        body: "The 'NPM Audit (Security Scan)' stage finished with status: ${env.AUDIT_STATUS}.\n\nSee attached log for details.",
                        attachmentsPattern: 'audit-log.txt'
                    )
                }
            }
        }
    }
}
