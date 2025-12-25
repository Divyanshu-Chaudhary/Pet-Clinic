pipeline {
    agent any

    tools {
        jdk 'JAVA'
        maven 'MAVEN'
    }

    environment {
        SONAR_HOME = tool 'sonar-scanner'
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
                    sh 'mvn -DskipTests org.sonarsource.scanner.maven:sonar-maven-plugin:3.10.0.2594:sonar'
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
                sh 'docker tag $DOCKER_IMAGE:$BUILD_NUMBER $DOCKER_IMAGE:latest'
            }
        }
        
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'divyanshu067',
                    passwordVariable: '@8CYQr2r92'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $DOCKER_IMAGE:$BUILD_NUMBER'
                    sh 'docker push $DOCKER_IMAGE:latest'
                }
            }
        }
        
        stage('Deploy') {
            steps {
                sh 'docker rm -f myapp || true'
                sh 'docker run -d --name myapp -p 8081:8080 $DOCKER_IMAGE:latest'
            }
        }
    }
    
    post {
        always {
            sh 'docker logout || true'
        }
    }
}
