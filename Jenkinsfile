@Library('shared-library') _

pipeline {
    agent any

    environment {
        AWS_REGION     = "ap-south-1"
        AWS_ACCOUNT_ID = "381492193486"
        ECR_REPO       = "my-app-repo"
        ROLE_ARN       = credentials('role-arn')
        IMAGE_TAG      = "${BUILD_NUMBER}"
    }

    stages {
        stage('OWASP Scan') {
            steps {
                owaspScan()
            }
        }
        stage('Assume Role') {
            steps {
                awsAssumeRole(env.ROLE_ARN, env.AWS_REGION)
            }
        }
        stage('Docker Build') {
            steps {
                dockerBuild(env.ECR_REPO, env.IMAGE_TAG)
            }
        }
        stage('Trivy Scan') {
            steps {
                trivyScan(env.ECR_REPO, env.IMAGE_TAG)
            }
        }
        stage('Push to ECR') {
            steps {
                ecrPush(env.ECR_REPO, env.IMAGE_TAG, env.AWS_ACCOUNT_ID, env.AWS_REGION)
            }
        }
    }
}
