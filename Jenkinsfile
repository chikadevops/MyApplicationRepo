pipeline {
    agent any
    tools {
        maven 'Maven3'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '26a7104f-e626-4c63-8f2e-ac4fdcb5a9bf', url: 'https://github.com/chikadevops/MyApplicationRepo/']])
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean install -f MyWebApp/pom.xml'
            }
        }
        
        stage('JaCoCo') {
            steps {
                jacoco()
            }
        }
        
        stage('Code Quality') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn -f MyWebApp/pom.xml sonar:sonar'
                }
            }
        }
    }
}
