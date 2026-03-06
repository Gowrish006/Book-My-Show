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
                        script {
                          sh """
                          ${tool 'sonar-scanner'}/bin/sonar-scanner \
                          -Dsonar.projectKey=Gowrish-BMS \
                          -Dsonar.sources=.
                          """
                        }
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

        stage('Push Docker Image to DockerHub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh 'docker push gowrish006/gowrish-bms:v1.0'
                    }
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                docker stop bookmyshow || true
                docker rm bookmyshow || true
                docker run -d -p 3000:3000 --name bookmyshow gowrish006/gowrish-bms:v1.0
                '''
            }
        }

    }

    post {

        success {
            emailext(
                subject: "Jenkins Build SUCCESS - BookMyShow",
                body: """
Pipeline executed successfully.

Project: BookMyShow
Build Number: ${env.BUILD_NUMBER}
Job Name: ${env.JOB_NAME}

Docker Image: gowrish006/gowrish-bms:v1.0
Application deployed successfully.

Access Application:
http://<JENKINS_SERVER_IP>:3000
""",
                to: "poolagowrish1920@gmail.com"
            )
        }

        failure {
            emailext(
                subject: "Jenkins Build FAILED - BookMyShow",
                body: """
Pipeline execution FAILED.

Project: BookMyShow
Build Number: ${env.BUILD_NUMBER}
Job Name: ${env.JOB_NAME}

Check Jenkins console logs for details.
""",
                to: "poolagowrish1920@gmail.com"
            )
        }

        always {
            echo "Pipeline finished."
        }
    }
}