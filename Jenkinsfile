pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        AWS_REGION = 'us-east-1'
        ACCOUNT_ID = '367842306906'
        ECR_REPO = 'java-app'
        IMAGE_NAME = 'java-devops-project'
        ECR_URI = '367842306906.dkr.ecr.us-east-1.amazonaws.com/java-app'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh '''
                    mvn org.sonarsource.scanner.maven:sonar-maven-plugin:5.8.0.7211:sonar \
                    -Dsonar.projectKey=java-devops-project
                    '''
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t java-devops-project .'
            }
        }

        stage('ECR Login') {
            steps {
                sh '''
                aws ecr get-login-password --region us-east-1 | \
                docker login --username AWS --password-stdin \
                367842306906.dkr.ecr.us-east-1.amazonaws.com
                '''
            }
        }

        stage('Push To ECR') {
            steps {
                sh '''
                docker tag java-devops-project:latest $ECR_URI:latest

                docker push $ECR_URI:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'Build, SonarQube, Docker Build and ECR Push Successful'
        }

        failure {
            echo 'Pipeline Failed here'
        }
    }
}
