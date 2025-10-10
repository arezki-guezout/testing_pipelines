pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps { git branch: "${BRANCH_NAME}", url: 'git@repo.git' }
        }

        stage('Build & Unit Tests') {
            steps { sh 'mvn clean test' }
        }

        stage('Build Docker Image') {
            steps { sh 'docker build -t registry/service:${GIT_COMMIT} .' }
        }

        stage('Deploy to Dev') {
            when { branch 'feature/*' }
            steps { sh './deploy.sh dev registry/service:${GIT_COMMIT}' }
        }

        stage('Smoke Tests Dev') {
            when { branch 'feature/*' }
            steps { sh './tests/smoke.sh dev' }
        }

        stage('Integration Env') {
            when { branch 'develop' }
            steps {
                sh './deploy.sh integration registry/service:${GIT_COMMIT}'
                sh './tests/integration.sh integration'
            }
        }

        stage('Recette Env (UAT)') {
            when { branch pattern: "release/.*", comparator: "REGEXP" }
            steps {
                sh './deploy.sh recette registry/service:${GIT_COMMIT}'
                sh './tests/tnr.sh recette'
            }
        }

        stage('Preprod & Prod') {
            when { branch 'main' }
            steps {
                sh './deploy.sh preprod registry/service:${GIT_COMMIT}'
                sh './tests/smoke.sh preprod'
                sh './tests/perf.sh preprod'
                input message: "Valider mise en Prod ?"
                sh './deploy.sh prod registry/service:${GIT_COMMIT}'
                sh './tests/smoke.sh prod'
            }
        }
    }
}

