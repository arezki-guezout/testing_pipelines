pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh './build.sh'
            }
        }

        stage('Deploy DEV') {
            steps {
                sh './deploy_dev.sh'
            }
        }

        stage('Integration Tests') {
            steps {
                sh './run_integration_tests.sh'
            }
        }

        stage('Deploy Recette') {
            steps {
                sh './deploy_recette.sh'
            }
        }

        stage('E2E + TNR Tests') {
            steps {
                sh './run_e2e_tests.sh'
            }
        }
    }
}
