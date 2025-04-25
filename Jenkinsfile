pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'sergeystudent/jenkins-demo'
    }
    stages {
        stage('Сборка Docker-образа') {
            steps {
                script {
                    echo 'Сборка Docker-образа'
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }
        stage('Просмотр образов') {
            steps {
                script {
                    echo 'Локальные Docker-образы'
                    sh 'docker images | grep kenkins-demo'
                }
            }
        }
    }
}