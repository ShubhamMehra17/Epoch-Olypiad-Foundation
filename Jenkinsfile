@Library('shared-library') _

pipeline {
    agent any

    tools {
        sonarRunner 'sonar-scanner'
    }

    options {
        timestamps()
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('sonar-local') {
                    sh """
                      sonar-scanner \
                        -Dsonar.projectKey=Epoch_Olympiad \
                        -Dsonar.sources=.
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dockerBuild(
                    imageName: 'epoch-olympiad',
                    imageTag: "${BUILD_NUMBER}"
                )
            }
        }
    }
}
