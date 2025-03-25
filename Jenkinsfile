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

        stage('Version, Flake8 & Tests') {
            parallel {
                stage('Set Version') {
                    steps {
                        script {
                            env.BUILD_VERSION = "1.0.${env.BUILD_NUMBER}"
                            echo "Building ${IMAGE_NAME} version ${env.BUILD_VERSION}"
                        }
                    }
                }
                stage('Flake8') {
                    steps {
                        script {
                            // Check Python and Flake8 versions, then run Flake8
                            sh '''
                                python3 --version
                                python3 -m flake8 --version
                                python3 -m flake8 . --count --show-source --statistics || true
                            '''
                        }
                    }
                }
                stage('Run Tests') {
                    steps {
                        script {
                            // Run the tests and save the report
                            sh 'pytest | tee report.txt'
                        }
                    }
                    post {
                        always {
                            // Archive test report as an artifact
                            archiveArtifacts artifacts: 'report.txt', fingerprint: true
                        }
                    }
                }
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'DOCKER_PASSWORD_RICARDO', variable: 'DOCKER_PASS')]) {
                        sh '''
                            docker build -t ${IMAGE_NAME}:${env.BUILD_VERSION} .
                            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                            docker push ${IMAGE_NAME}:${env.BUILD_VERSION}
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed."
        }
    }
}
