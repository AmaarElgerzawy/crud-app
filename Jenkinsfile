pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'SonarQube-Scanner'
    }

    stages {

        stage('Code-Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
$SCANNER_HOME/bin/sonar-scanner \
-Dsonar.projectKey=jenkins-project \
-Dsonar.sources=. \
-Dsonar.login=$SONAR_AUTH_TOKEN
'''
                }
            }
        }

        stage('Docker Build And Push') {
            steps {
                script {
                    docker.withRegistry('', 'DockerHub') {
                        def image = docker.build("ammarmohamed805/ammar-cicd-aws")
                        image.push("latest")
                    }
                }
            }
        }

        stage('Deploy To EC2') {
            steps {
                sh 'docker rm -f app || true'
                sh 'docker run -d --name app -p 3000:3000 ammarmohamed805/ammar-cicd-aws:latest'
            }
        }
    }
}
