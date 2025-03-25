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
        stage('Flake & Tests') {
            parallel {
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
                }
            }
        }
        stage('Docker Image') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'DOCKER_PASSWORD_RICARDO', variable: 'DOCKER_PASS')]) {
                        sh """
                            docker build -t ${IMAGE_NAME}:${env.BUILD_VERSION} .
                            docker login -u $DOCKER_USER -p $DOCKER_PASS
                            docker push ${IMAGE_NAME}:${env.BUILD_VERSION}
                        """
                    }
                }
            }
        }

    }
}
