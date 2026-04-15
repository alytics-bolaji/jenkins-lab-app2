pipeline {
    agent any
 
    environment {
        APP_NAME = 'jenkins-lab-app'
        VERSION  = '1.0.0'
    }
 
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }
 
        stage('Environment Info') {
            steps {
                echo "App: ${env.APP_NAME} v${env.VERSION}"
                echo "Branch: ${env.GIT_BRANCH}"
                echo "Build #: ${env.BUILD_NUMBER}"
                sh 'java -version || echo No Java installed'
            }
        }
 
        stage('Build') {
            steps {
                echo 'Compiling application...'
                sh 'echo Build complete > build-output.txt'
            }
        }
 
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'chmod +x test.sh'
                sh './test.sh'
            }
        }
 
        stage('Package') {
            steps {
                echo 'Packaging application...'
                sh "echo ${env.APP_NAME}-${env.VERSION} > package-name.txt"
                archiveArtifacts artifacts: '*.txt', fingerprint: true
            }
        }
    }
 
    post {
        success {
            echo 'Pipeline SUCCEEDED!'
        }
        failure {
            echo 'Pipeline FAILED!'
        }
        always {
            echo 'Pipeline finished. Cleaning workspace...'
            cleanWs()
        }
    }
}

