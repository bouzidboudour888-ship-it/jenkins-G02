pipeline {
    agent any

    stages {
        stage('Cloner le depot') {
            steps {
                git url: 'https://github.com/bouzidboudour888-ship-it/jenkins-G02.git',
                    branch: 'main'
            }
        }

        stage('Verification du depot') {
            steps {
                bat 'git status'
            }
        }

        stage('Afficher le contenu du projet') {
            steps {
                bat 'dir'
            }
        }

        stage('Simuler un deploiement local') {
            steps {
                bat 'echo Le fichier index.html est pret a etre affiche'
            }
        }

        stage('Fin du build') {
            steps {
                bat 'echo SUCCESS'
            }
        }
    }

    post {
        success {
            echo 'Build OK'
        }
        failure {
            echo 'Build KO'
        }
    }
}