pipeline{
  agent { label 'Jenkins-Agent'}
  tools {
   jdk 'Java21'
   maven 'Maven3'
}
  stages{
    stage("Cleanup Workspace"){
            steps{
            cleanWs()
              }
      }
    stage("Checkout from SCM"){
            steps{
            git branch: 'main', credentialsId: 'github', url: 'https://github.com/rahulgpt2202/registration-app.git'
              }
      }
    stage("Build Application"){
            steps{
                 sh "mvn clean package"
              }
      }
    stage("Test Application"){
            steps{
                 sh "mvn test"
              }
      }

    stage('SonarQube Analysis') {
    steps {
        withSonarQubeEnv('sonarqube-server') {
            sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.token=YOUR_SONAR_TOKEN'
        }
    }
}
        
    }
}
