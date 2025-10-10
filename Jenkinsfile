pipeline {
    agent any

    environment {
        IMAGE = "registry/myservice:${GIT_COMMIT}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: "${BRANCH_NAME}", url: 'git@github.com:org/myservice.git'
            }
        }

        stage('Build & Unit Tests') {
            steps {
                sh 'mvn clean test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE} ."
            }
        }

        // ============ Feature branches ============ //
        stage('Deploy to Dev & Smoke Tests') {
            when { branch pattern: "feature/.*", comparator: "REGEXP" }
            steps {
                sh "./deploy.sh dev ${IMAGE}"
                sh "./tests/smoke.sh dev"
            }
        }

        // ============ Develop branch ============ //
        stage('Deploy to Integration & Run Tests') {
            when { branch 'develop' }
            steps {
                sh "./deploy.sh integration ${IMAGE}"
                sh "./tests/smoke.sh integration"
                sh "./tests/integration.sh integration"
                sh "./tests/tnr_subset.sh integration"
            }
        }

        // ============ Release branches ============ //
        stage('Deploy to Recette (UAT)') {
            when { branch pattern: "release/.*", comparator: "REGEXP" }
            steps {
                sh "./deploy.sh recette ${IMAGE}"
                sh "./tests/smoke.sh recette"
                sh "./tests/tnr_full.sh recette"
                sh "./tests/uat_manual_validation.sh"
            }
        }

        // ============ Hotfix branches ============ //
        stage('Hotfix Preprod & Prod') {
            when { branch pattern: "hotfix/.*", comparator: "REGEXP" }
            steps {
                sh "./deploy.sh preprod ${IMAGE}"
                sh "./tests/smoke.sh preprod"
                input message: "Hotfix validé pour Prod ?"
                sh "./deploy.sh prod ${IMAGE}"
                sh "./tests/smoke.sh prod"
            }
        }

        // ============ Main branch (prod release) ============ //
        stage('Preprod Deployment & Tests') {
            when { branch 'main' }
            steps {
                sh "./deploy.sh preprod ${IMAGE}"
                sh "./tests/smoke.sh preprod"
                sh "./tests/perf.sh preprod"
                sh "./tests/security.sh preprod"
            }
        }

        stage('Production Deployment') {
            when { branch 'main' }
            steps {
                input message: "Valider mise en production ?"
                sh "./deploy.sh prod ${IMAGE}"
                sh "./tests/smoke.sh prod"
            }
        }
    }

    post {
        always {
            junit '**/target/surefire-reports/*.xml'
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
        }
    }
}
