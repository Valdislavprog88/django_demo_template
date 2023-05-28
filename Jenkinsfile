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
            agent any
            steps {
                withCredentials([usernamePassword(credentialsId: 'evmexaDockerCred', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'docker login -u ${USERNAME} -p ${PASSWORD}'
                    sh 'docker push evmexaprog88/django_jenkins:${GIT_COMMIT}'
                    sh 'docker push evmexaprog88/django_jenkins:latest'
                }
            }
        }
        stage("deploy") {
            agent any
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'deploy_server', keyFileVariable: 'KEY_FILE', usernameVariable:'USERNAME')]) {
                    sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@evmeshkin.devops.io12.me mkdir -p ~${WORKSPACE}'
                    sh 'scp -o StrictHostKeyChecking=no -i ${KEY_FILE} docker-compose.yaml ${USERNAME}@evmeshkin.devops.io12.me:~${WORKSPACE}'
                    sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@evmeshkin.devops.io12.me docker-compose -f ~${WORKSPACE}/docker-compose.yaml pull'
                    sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@evmeshkin.devops.io12.me docker-compose -f ~${WORKSPACE}/docker-compose.yaml up -d'
                    sh 'scp -o StrictHostKeyChecking=no -i ${KEY_FILE} evmeshkin.devops.io12.me.conf ${USERNAME}@devops.io12.me:~/nginx/'
                    sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@devops.io12.me sudo systemctl reload nginx'
                    sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@devops.io12.me sudo certbot --nginx --non-interactive --agree-tos -m paramonov@informatics.ru -d evmeshkin.devops.io12.me'
                }
            }
        }

    }
}
