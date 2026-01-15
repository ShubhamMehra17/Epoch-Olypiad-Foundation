@Library('shared-library') _

pipeline {
    agent any

    options {
        timestamps()
    }

    parameters {
        choice(
            name: 'RUN_STAGE',
            choices: ['FULL_PIPELINE', 'ONLY_TRIVY_SCAN'],
            description: 'Select which part of the pipeline to run'
        )
        string(
            name: 'IMAGE_TAG',
            defaultValue: '',
            description: 'Docker image tag to scan (leave empty to use BUILD_NUMBER)'
        )
    }

    stages {

        stage('Checkout') {
            when {
                expression { params.RUN_STAGE == 'FULL_PIPELINE' }
            }
            steps {
                checkout scm
            }
        }

        stage('SonarQube Scan') {
            when {
                expression { params.RUN_STAGE == 'FULL_PIPELINE' }
            }
            steps {
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
            when {
                expression { params.RUN_STAGE == 'FULL_PIPELINE' }
            }
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
                    params.RUN_STAGE == 'FULL_PIPELINE' ||
                    params.RUN_STAGE == 'ONLY_TRIVY_SCAN'
                }
            }
            steps {
                script {
                    def finalTag = params.IMAGE_TAG?.trim()
                        ? params.IMAGE_TAG
                        : "${BUILD_NUMBER}"

                    echo "🔍 Scanning image epoch-olympiad:${finalTag}"

                    trivyScan(
                        imageName: 'epoch-olympiad',
                        imageTag: finalTag,
                        severity: 'CRITICAL,HIGH',
                        failBuild: true
                    )
                }
            }
        }
    }
}
