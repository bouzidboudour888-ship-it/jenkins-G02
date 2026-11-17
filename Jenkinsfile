pipeline {
    agent any

    options {
        disableConcurrentBuilds()
    }

    environment {
        IMAGE = 'boudourbouzid/monapp'
        TAG = "build-${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker version'
                bat 'docker build -t %IMAGE%:%TAG% .'
            }
        }

        stage('Smoke Test') {
            steps {
                bat(
                    returnStatus: true,
                    script: 'docker rm -f monapp_test'
                )

                bat 'docker run -d --name monapp_test -p 8081:80 %IMAGE%:%TAG%'

                bat 'curl.exe --fail --retry 10 --retry-connrefused --retry-delay 2 http://localhost:8081/ -o NUL'
            }

            post {
                always {
                    bat(
                        returnStatus: true,
                        script: 'docker rm -f monapp_test'
                    )
                }
            }
        }

        stage('Push Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_PASS'
                    )
                ]) {
                    bat '''
                        @echo off
                        powershell -NoProfile -Command "$env:DOCKERHUB_PASS | docker login --username $env:DOCKERHUB_USER --password-stdin; exit $LASTEXITCODE"
                    '''

                    bat 'docker tag %IMAGE%:%TAG% %IMAGE%:latest'
                    bat 'docker push %IMAGE%:%TAG%'
                    bat 'docker push %IMAGE%:latest'
                }
            }
        }
    }

    post {
        success {
            echo 'Build + Test + Push OK'
        }

        failure {
            echo 'Build, test ou push en echec'
        }
    }
}