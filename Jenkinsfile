pipeline {
    agent any

    tools {
        jdk 'JAVA'
        maven 'MAVEN'
    }

    environment {
        IMAGE_NAME = "divyanshu067/petclinic"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Divyanshu-Chaudhary/Pet-Clinic.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                withDockerRegistry(credentialsId: 'dockerhub-creds', url: 'https://index.docker.io/v1/') {
                    sh '''
                    docker build -t $IMAGE_NAME:latest .
                    docker push $IMAGE_NAME:latest
                    '''
                }
            }
        }
    }
}
