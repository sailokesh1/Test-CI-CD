pipeline {
    agent any
    
    environment {
        APP_NAME = "my-sample-app"
        APP_VERSION = "1.0.${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out code..."
                git branch: 'main', url: 'https://github.com/your-org/your-repo.git'
            }
        }

        stage('Build & Test (Parallel)') {
            parallel {
                stage('Build') {
                    steps {
                        echo "Building ${APP_NAME} version ${APP_VERSION}..."
                        sh 'mvn clean package -DskipTests'
                        archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
                    }
                }
                stage('Test') {
                    steps {
                        echo "Running unit tests..."
                        sh 'mvn test'
                    }
                }
            }
        }

        stage('Quality Checks') {
            steps {
                echo "Running code quality checks..."
                sh 'echo "Static analysis passed ✅"'
                // e.g., integrate SonarQube or linting here
            }
        }

        stage('Approval') {
            steps {
                script {
                    input message: "Deploy ${APP_NAME} version ${APP_VERSION} to production?", ok: "Deploy"
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying ${APP_NAME}:${APP_VERSION}..."
                sh 'scp target/*.jar user@server:/opt/app/'
                sh 'ssh user@server "systemctl restart my-app"'
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully ✅"
            // Example: Slack notifier plugin could be used here
        }
        failure {
            echo "Pipeline failed ❌"
        }
        always {
            echo "Cleaning up workspace..."
            cleanWs()
        }
    }
}
