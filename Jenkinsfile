pipeline {
    agent none
    stages {
        stage("test") {
            agent {
                docker {
                    image 'python:3.10'
                }
            }
            steps {
                sh '''
                    python -m venv venv
                    . venv/bin/activate
                    pip install -r requirements.txt
                    python manage.py test

                '''
            }
        }
        stage("build") {
            agent any
            steps {
                sh 'docker build . -t evmexaprog88/django_jenkins:${GIT_COMMIT} -t evmexaprog88/django_jenkins:latest'
            }
        }
        stage("push") {
            withCredentials([usernamePassword(credentialsId: 'evmexaDockerCred',
            usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                sh 'docker login -u ${USERNAME} -p ${PASSWORD}'
                sh 'docker push evmexaprog88/django_jenkins:${GIT_COMMIT}'
                sh 'docker push evmexaprog88/django_jenkins:latest'
            }
        }

    }
}
