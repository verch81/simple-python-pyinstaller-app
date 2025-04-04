pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') { 
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py' 
                stash(name: 'compiled-results', includes: 'sources/*.py*') 
            }
        }
        stage('Test') {
            steps {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                sh "pyinstaller --onefile sources/add2vals.py"
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }        
    }
    post {
        success {
            emailext(
                subject: 'SUCCESS: Job ${JOB_NAME} Build #${BUILD_NUMBER}',
                body: ''' Scope: Hillel homework #31.
Good news! 
The build was successful.

Job Name: ${JOB_NAME}
Build Number: ${BUILD_NUMBER}
Build URL: ${BUILD_URL}''',
                to: 'vierovkin@gmail.com'
            )
        }
        failure {
            emailext(
                subject: 'FAILURE: Job ${JOB_NAME} Build #${BUILD_NUMBER}',
                body: ''' Scope: Hillel homework #31.
Oops! 

The build failed.

Job Name: ${JOB_NAME}
Build Number: ${BUILD_NUMBER}
Build URL: ${BUILD_URL}''',
                to: 'vierovkin@gmail.com'
            )
        }
    }
}
