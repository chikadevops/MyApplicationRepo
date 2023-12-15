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
        
        stage('Nexus Upload') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'MyWebApp', classifier: '', file: 'MyWebApp/target/MyWebApp.war', type: 'war']], credentialsId: '897de581-aca3-4f9f-bb8b-a7175d56be2b', groupId: 'MyWebApp', nexusUrl: 'ec2-54-91-130-9.compute-1.amazonaws.com:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
            }
        }
    }
}
