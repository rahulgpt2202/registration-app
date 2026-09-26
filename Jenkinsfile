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
            DOCKER_PASS = 'Jay@092773271'
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
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
                // Fast build without redundant test execution
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube-server') {
                    // Plugin version is managed via pom.xml
                    sh 'mvn sonar:sonar -Dsonar.token=sqa_542947a4ca3d34b817b47d2f680d3cd988b611ad'
                }
            }
        }

        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
                    }
                }
            
        }

        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                   docker.withRegistry('', DOCKER_PASS) {
                       docker_image.push("${IMAGE_TAG}")
                       docker_image.push('latest')
            }
        }
    }
}
    }
}
