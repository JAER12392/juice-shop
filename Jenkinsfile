// Jenkinsfile solution based on the pipeline plugin:
// https://www.jenkins.io/solutions/pipeline/

// Results are output in SARIF format & processed using the Warnings NG plugin:
// https://plugins.jenkins.io/warnings-ng/

// Please read the contents of this file carefully & ensure the URLs, tokens etc match your organisations needs.

pipeline {
    agent any

    // Requires a configured NodeJS installation via https://plugins.jenkins.io/nodejs/
    tools { nodejs 'nodejs' }

    stages {

        // Install the Snyk CLI with npm. For more information, check:
        // https://docs.snyk.io/snyk-cli/install-the-snyk-cli
        stage('Install snyk CLI') {
            steps {
                script {
                    sh 'npm install -g snyk'
                }
            }
        }

        // Authorize the Snyk CLI
        stage('Authorize Snyk CLI') {
            steps {
                withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                    sh 'snyk auth ${SNYK_TOKEN}'
                }
            }
        }

           // stage('Build App') {
          // steps {
                 // sh 'Building..'
                 // sh 'npm install'
           // }
      //  }

        stage('Snyk') {
            parallel {
                stage('Snyk Open Source') {
                    steps {
                        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                            sh 'snyk test --sarif-file-output=results-open-source.sarif'
                        }
                        recordIssues tool: sarif(name: 'Snyk Open Source', id: 'snyk-open-source', pattern: 'results-open-source.sarif')
                    }
                }
                stage('Snyk Code') {
                    steps {
                        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                            sh 'snyk code test --sarif-file-output=results-code.sarif'
                        }
                        recordIssues  tool: sarif(name: 'Snyk Code', id: 'snyk-code', pattern: 'results-code.sarif')
                    }
                }
                
            }
        }
    }
} 
