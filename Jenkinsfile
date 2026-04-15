pipeline {
    agent any
 
    options {
        // Abort the build if it takes longer than 10 minutes
        timeout(time: 10, unit: 'MINUTES')
        // Keep only the last 5 builds to save disk space
        buildDiscarder(logRotator(numToKeepStr: '5'))
        // Add timestamps to every log line
        timestamps()
        // Do not run concurrent builds of the same job
        disableConcurrentBuilds()
    }
 
    environment {
        APP_NAME    = 'jenkins-lab-app'
        DEPLOY_ENV  = 'staging'
    }
 
    stages {
 
        stage('Checkout') {
            steps { checkout scm }
        }
 
        // Parallel stage: lint and unit tests run at the same time
        stage('Validate') {
            parallel {
                stage('Lint') {
                    steps {
                        echo 'Running linter...'
                        sh 'echo No lint errors found'
                    }
                }
                stage('Unit Tests') {
                    steps {
                        echo 'Running unit tests...'
                        sh 'chmod +x test.sh'
                        sh './test.sh'
                    }
                }
                stage('Security Scan') {
                    steps {
                        echo 'Running security scan...'
                        sh 'echo No vulnerabilities found'
                    }
                }
            }
        }

        stage('Debug') {
            steps { echo "BRANCH_NAME: ${env.BRANCH_NAME}"}
        }
 
        stage('Build') {
            steps {
                sh 'echo Building application...'
                sh 'echo Build artifact > app.jar'
                archiveArtifacts artifacts: 'app.jar'
            }
        }
 
        // This stage only runs on the main branch
        stage('Deploy to Staging') {
            when {
                branch 'main'
            }
            steps {
                echo "Deploying to ${env.DEPLOY_ENV}..."
                sh 'echo Deployment complete'
            }
        }
 
        // Manual approval gate before production deployment
        stage('Approve for Production') {
            when { branch 'main' }
            steps {
                input message: 'Deploy to production?',
                      ok: 'Yes, deploy!',
                      submitter: 'admin'
            }
        }
 
        stage('Deploy to Production') {
            when { branch 'main' }
            steps {
                echo 'Deploying to production!'
                sh 'echo Production deploy complete'
            }
        }
    }
 
    post {
        success {
            echo "SUCCESS: ${env.APP_NAME} build #${env.BUILD_NUMBER} passed."
        }
        failure {
            echo "FAILURE: ${env.APP_NAME} build #${env.BUILD_NUMBER} failed!"
        }
        unstable {
            echo 'Build is unstable — check test results.'
        }

        always {
            cleanWs()
        }
    }
}
 














// pipeline {
//     agent any
 
//     environment {
//         APP_NAME = 'jenkins-lab-app'
//         VERSION  = '1.0.0'
//     }
 
//     stages {
//         stage('Checkout') {
//             steps {
//                 echo 'Checking out source code...'
//                 checkout scm
//             }
//         }
 
//         stage('Environment Info') {
//             steps {
//                 echo "App: ${env.APP_NAME} v${env.VERSION}"
//                 echo "Branch: ${env.GIT_BRANCH}"
//                 echo "Build #: ${env.BUILD_NUMBER}"
//                 sh 'java -version || echo No Java installed'
//             }
//         }
 
//         stage('Build') {
//             steps {
//                 echo 'Compiling application...'
//                 sh 'echo Build complete > build-output.txt'
//             }
//         }
 
//         stage('Test') {
//             steps {
//                 echo 'Running tests...'
//                 sh 'chmod +x test.sh'
//                 sh './test.sh'
//             }
//         }
 
//         stage('Package') {
//             steps {
//                 echo 'Packaging application...'
//                 sh "echo ${env.APP_NAME}-${env.VERSION} > package-name.txt"
//                 archiveArtifacts artifacts: '*.txt', fingerprint: true
//             }
//         }
//     }
 
//     post {
//         success {
//             echo 'Pipeline SUCCEEDED!'
//         }
//         failure {
//             echo 'Pipeline FAILED!'
//         }
//         always {
//             echo 'Pipeline finished. Cleaning workspace...'
//             cleanWs()
//         }
//     }
// }

