pipeline {
    agent {
        label 'docker'
    }
    stages {
        stage('Source') {
            steps {
                echo 'Git'
                git 'https://github.com/srayuso/unir-cicd.git'
            }
        }
        stage('Build') {
            steps {
                echo 'Ejecutandoce'
                sh 'make build'
            }
        }
        stage('Tests') {
            parallel {
                stage('Unit tests') {
                    steps {
                        echo 'Test Unit'
                        sh 'make test-unit'
                        archiveArtifacts artifacts: 'results/unit/*.xml'
                    }
                }
                stage('API tests') {
                    steps {
                        echo 'Test API'
                        sh 'make test-api'
                        archiveArtifacts artifacts: 'results/api/*.xml'
                    }
                }
                stage('E2E tests') {
                    steps {
                        echo 'Test E2E'
                        sh 'make test-e2e'
                        archiveArtifacts artifacts: 'results/e2e/*.xml'
                    }
                }
            }
        }
    }
    post {
        always {
            script {
                echo "Pipeline terminó de ejecutar el ${currentBuild.currentResult}"
            }
            junit 'results/unit/*.xml'
            junit 'results/api/*.xml'
            junit 'results/e2e/*.xml'
            cleanWs()
        }
        success {
            publishHTML([
                reportName: 'Unit Test Reporte',
                reportDir: 'results/unit',
                reportFiles: 'index.html',
                alwaysLinkToLastBuild: true,
                keepAll: true
            ])
            publishHTML([
                reportName: 'API Test Reporte',
                reportDir: 'results/api',
                reportFiles: 'index.html',
                alwaysLinkToLastBuild: true,
                keepAll: true
            ])
            publishHTML([
                reportName: 'E2E Test Reporte',
                reportDir: 'results/e2e',
                reportFiles: 'index.html',
                alwaysLinkToLastBuild: true,
                keepAll: true
            ])
            emailext (
                subject: "Pipeline Exitoso: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """<p>Pipeline ${env.JOB_NAME} #${env.BUILD_NUMBER} se ha completado correctamente..</p>
                         <p>Consulte la consola en Jenkins para obtener más detalles.</p>""",
                to: 'chimalleobardo@gmail.com'
            )
        }
        failure {
            emailext (
                subject: "Pipeline Fallido: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """<p>Pipeline ${env.JOB_NAME} #${env.BUILD_NUMBER} ha fallado.</p>
                         <p>Consulte la consola en Jenkins para obtener más detalles.</p>""",
                to: 'chimalleobardo@gmail.com'
            )
        }
    }
}
