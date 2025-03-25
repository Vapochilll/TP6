pipeline {
   agent any

   environment {
    IMAGE_NAME = 'vapochilll/my-python-project'
    DOCKER_USER = 'vapochilll'
   } 
    stages {
        stage('Checkout Code') {
            steps {
                 script {
                        // Checkout from a specific Git repository and branch
                        git branch: 'main', url: 'https://github.com/Vapochilll/my-python-project.git'
                    }
            }
        }
        stage('Version & Flake & Tests') {
            parallel {
               stage('Init version') { 
                    steps { 
                         script {
                           env.BUILD_VERSION = "1.0.${env.BUILD_NUMBER}"
                           echo "Building ${IMAGE_NAME} version ${env.BUILD_VERSION}"
                         }
                    } 
                } 
                stage('Flake') {
                    steps {
                        script {
                            // Run Python script (make sure Python is installed)
                            sh 'python3 --version'
                            sh 'python3 -m flake8 --version'
                            sh 'python3 -m flake8 . --count --show-source --statistics || true'
                        }
                    }
                }
                stage('Tests') {
                    steps {
                        script {
                            // Run Python script (make sure Python is installed)
                            sh 'pytest | tee report.txt'
                        }
                    }
                    post {
                        always {
                            // rendre accessible le rapport
                            archiveArtifacts artifacts: 'report.txt', fingerprint: true
                        }
                    }
                }
            }
        }
        stage('Docker Image') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'DOCKER_PASSWORD_RICARDO', variable: 'DOCKER_PASS')]) {
                        sh """
                            docker build -t ${IMAGE_NAME}:${env.BUILD_VERSION} .
                            echo --key $DOCKER_PASS|docker login -u $DOCKER_USER --password-stdin
                            docker push ${IMAGE_NAME}:${env.BUILD_VERSION}
                        """
                    }
                }
            }
        }

    }
    post {
        success {
            echo "Pipeline terminé!"
        }
        failure {
            echo "Pipeline échoué."
        }
    }
}
