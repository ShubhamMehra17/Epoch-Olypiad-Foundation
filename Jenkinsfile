pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('sonar-local') {
                    sh '''
                      sonar-scanner \
                        -Dsonar.projectKey=Epoch_Olympiad \
                        -Dsonar.sources=.
                    '''
                }
            }
        }
    }
}
