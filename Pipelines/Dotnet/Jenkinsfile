pipeline {
    agent any
    
    stages {
        stage('Clone') {
            steps {
                echo "Clone repository..."
                git(
                    url: 'https://github.com/jeftegoes/ExampleUnitTestWithXUnit.git',
                    branch: 'master'
                )
            }
        }
        stage('Restore packages') {
            steps {
                sh 'dotnet restore'
            }
        }
        stage('Clean') {
            steps {
                sh 'dotnet clean'
            }
        }
        stage('Build') {
            steps {
                sh 'dotnet build'
            }
        }
        stage('Test') {
            steps {
                sh 'dotnet test'
            }
        }
        stage('Publish') {
            steps {
                sh 'dotnet publish -c Release'
            }
        }
        stage('Email') {
            steps {
                emailext body: '<b>Status: $BUILD_STATUS</b><br>Project: $PROJECT_NAME <br>Build Number: $BUILD_NUMBER <br> URL de build: $BUILD_URL', subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', to: 'jefte.goes@hotmail.com'
            }
        }
    }
    
    post {
        always {
            archiveArtifacts artifacts: 'Business/bin/Release/net5.0/publish/*.*', allowEmptyArchive: true
        }
        success {
            echo 'Success'
        }
        failure {
            echo 'Failure, send email notification...'
            mail bcc: '', body: "<b>Example</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "ERROR CI: Project name -> ${env.JOB_NAME}", to: "jefte.goes@hotmail.com";
        }
    }
}