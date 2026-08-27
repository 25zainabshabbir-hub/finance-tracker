pipeline {
    agent any

    tools {
        nodejs 'NodeJS-18' // Must match the Name configured in Manage Tools
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Backend Build') {
            steps {
                dir('backend') {
                    sh 'npm ci'
                    sh 'npm run build'
                }
            }
        }
    }

    post {
        success {
            slackSend(
                tokenCredentialId: 'slack-webhook',
                channel: 'C0BN7DM3LG6',
                color: 'good',
                message: "Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            )
        }
        failure {
            slackSend(
                tokenCredentialId: 'slack-webhook',
                channel: 'C0BN7DM3LG6',
                color: 'danger',
                message: "Build FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            )
        }
    }
}