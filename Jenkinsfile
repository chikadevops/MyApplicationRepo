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
                nexusArtifactUploader artifacts: [[artifactId: 'MyWebApp', classifier: '', file: 'MyWebApp/target/MyWebApp.war', type: 'war']], credentialsId: '897de581-aca3-4f9f-bb8b-a7175d56be2b', groupId: 'MyWebApp', nexusUrl: 'ec2-18-205-160-98.compute-1.amazonaws.com:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
            }
        }
        
        stage('DEV Deploy') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'c43d606a-b19e-4ff6-99e8-af1561b3cea4', path: '', url: 'http://ec2-3-84-82-204.compute-1.amazonaws.com:8080/')], contextPath: null, war: '**/*.war'
            }
        }
        
        stage('Slack Notify') {
            steps {
                echo "Deployed to DEV Env successfully"
                slackSend channel: 'devops-tutorials', message: 'DEV deployment was successful'
            }
        }
        
        stage('DEV Approve') {
            steps {
                echo "Taking approval from QA manager"
                timeout(time: 7, unit: 'DAYS') {
                input message: 'Do you want to proceed to PROD?', submitter: 'admin,manager_userid'
                }
            }
        }
        
        stage('QA Deploy') {
            steps {
                echo "deploying to QA Env "
                deploy adapters: [tomcat9(credentialsId: 'c43d606a-b19e-4ff6-99e8-af1561b3cea4', path: '', url: 'http://ec2-3-84-82-204.compute-1.amazonaws.com:8080/')], contextPath: null, war: '**/*.war'
            }
        }
        
        stage('QA Approve') {
            steps {
                echo "Taking approval from QA manager"
                timeout(time: 7, unit: 'DAYS') {
                input message: 'Do you want to proceed to PROD?', submitter: 'admin,manager_userid'
                }
            }
        }
        
       stage('Slack Notify-QA') {
            steps {
                echo "Deployed to QA successfully"
                slackSend channel: 'devops-tutorials', message: 'QA deployment was successful, please start your testing'
            }
        }
    }
}
