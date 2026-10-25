pipeline {
    agent any

    tools {
        maven 'Maven3'
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
                withSonarQubeEnv('SonarQube') {
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
    }

    post {
        success {
            echo 'Build, SonarQube, and Docker Build Successful'
        }

        failure {
            echo 'Pipeline Failed'
        }
    }
}
