@Library('shared-library') _

pipeline {
    agent any


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
            steps{
                   script {
                        def scannerHome = tool 'sonar-scanner'
                        withSonarQubeEnv('sonar-local') {
                            sh """
                              ${scannerHome}/bin/sonar-scanner \
                                -Dsonar.projectKey=Epoch_Olympiad \
                                -Dsonar.sources=.
                            """
                        }
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
