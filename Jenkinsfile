pipeline {
    agent any

    environment {
        SNYK_TOKEN = credentials('snyk-token')
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing..'
                sh 'npm test'
            }
        }

        stage('Security') {
            steps {
                echo 'Scanning with Snyk..'
                script {
                    docker.image('snyk/snyk:latest').inside() {
                        sh '''
                        snyk auth $SNYK_TOKEN
                        snyk test --json > snyk_report.json
                        '''
                    }
                    sh 'npm install -g snyk-to-html'
                    sh 'cat snyk_report.json | snyk-to-html > snyk_report.html'
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'snyk_report.json, snyk_report.html', fingerprint: true
        }
    }
}
