pipeline {
    agent any

    stages {   
        stage('Build with Maven') {
            steps {
                sh 'cd SampleWebApp && mvn clean install'
            }
        }
        
        stage('Test') {
            steps {
                sh 'cd SampleWebApp && mvn test'
            }
        }
        
        stage('Code Quality Scan') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh "mvn -f SampleWebApp/pom.xml sonar:sonar"
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
        
        stage('Push to Nexus') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'SampleWebApp', classifier: '', file: 'SampleWebApp/target/SampleWebApp.war', type: 'war']], 
                credentialsId: 'nexuspass', 
                groupId: 'SampleWebApp', 
                nexusUrl: 'http://ec2-3-94-110-211.compute-1.amazonaws.com:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'maven-snapshots', 
                version: '1.0-SNAPSHOT'
            }
        }
        
        stage('Deploy to Tomcat') {
            steps {
                deploy adapters: [
                    tomcat9(credentialsId: 'tompass', path: '', url: 'http://18.215.176.65:8080/')
                ], 
                contextPath: 'myapp', 
                war: 'SampleWebApp/target/SampleWebApp.war'
            }
        }
    }
}
