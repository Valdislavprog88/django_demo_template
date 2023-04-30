pipeline {
    agent {
        docker { image 'python:3.10' }
    }
    stages {
        stage('first') {
            steps {
                sh '''
                    python -m venv venv
                    . venv/bin/activate
                    pip install -r requirements.txt
                    python manage.py test
                '''
            }
        }
    }
}