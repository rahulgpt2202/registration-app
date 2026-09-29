pipeline {
    agent { label 'Jenkins-Agent' }
    
    tools {
        jdk 'Java21'
        maven 'Maven3'
    }
    
    environment {
        APP_NAME = "register-app-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "rahak2202"
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/rahulgpt2202/registration-app.git'
            }
        }

        stage("Build Application") {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube-server') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage("Build & Push Docker Image") {
            steps {
                script {
                    // 'dockerhub' matches the exact credential ID saved in Jenkins
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
                        def dockerImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                        dockerImage.push("${IMAGE_TAG}")
                        dockerImage.push('latest')
                    }
                }
            }
        }

    post {
        always {
            node('Jenkins-Agent') {
                cleanWs()
            }
        }
    }
}
