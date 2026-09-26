pipeline {
    agent { label 'Jenkins-Agent' }
    
    tools {
        jdk 'Java21'
        maven 'Maven3'
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
                sh "mvn clean package -DskipTests"
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
                    timeout(time: 2, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true, credentialsId: 'jenkins-sonarqube-token'
                    }
                }
            }
        }
    }
}
