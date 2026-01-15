@Library('shared-library') _

pipeline {
    agent any


    options {
        timestamps()
    }
    
    parameters {
        choice(
            name: 'RUN_STAGE',
            choices: [
                'FULL_PIPELINE',
                'ONLY_TRIVY_SCAN'
            ],
            description: 'Select which part of the pipeline to run'
        )
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

       stage('Trivy Image Scan') {
           when {
                expression {
                    params.RUN_STAGE == 'ONLY_TRIVY_SCAN'
                }
            }
            steps {
                trivyScan(
                    imageName: 'epoch-olympiad',
                    imageTag: "${BUILD_NUMBER}",
                    severity: 'CRITICAL,HIGH',
                    failBuild: true
                )
            }
        }
    }
}
