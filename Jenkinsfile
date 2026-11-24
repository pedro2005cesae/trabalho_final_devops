pipeline {
    agent { label 'ci-python-docker' }
    options {
        timeout(time: 15, unit: 'MINUTES')
        disableConcurrentBuilds()
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Tests') {
            steps {
                sh '''
                    python3 -m venv .venv
                    .venv/bin/python -m pip install -r requirements.txt -r requirements-dev.txt
                    .venv/bin/python -m pytest -q
                '''
            }
        }
        stage('Docker Build') {
            steps {
                sh '''
                    docker build -t "natixis-support:ci-${BUILD_NUMBER}" .
                    docker image inspect "natixis-support:ci-${BUILD_NUMBER}" --format '{{.Id}}'
                '''
            }
        }
    }
}
