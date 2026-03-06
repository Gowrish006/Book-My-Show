pipeline {
    agent any

    tools {
        nodejs 'node23'
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'devops-implementation', url: 'https://github.com/Gowrish006/Book-My-Show.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('bookmyshow-app') {
                    sh 'npm install'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('bookmyshow-app') {
                    withSonarQubeEnv('sonar-server') {
                        sh """
                        ${tool 'sonar-scanner'}/bin/sonar-scanner \
                        -Dsonar.projectKey=Gowrish-BMS \
                        -Dsonar.sources=.
                        """
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('bookmyshow-app') {
                    sh 'docker build -t gowrish006/gowrish-bms:v1.0 .'
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh 'docker run -d -p 3000:3000 --name bookmyshow gowrish006/gowrish-bms:v1.0'
            }
        }

    }
}