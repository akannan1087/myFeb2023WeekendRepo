pipeline {
    agent { 
       label "slave1"
    }

    tools {
        maven "Maven3"
    }
    
    stages {        
        stage ("Build") {
            steps {
                sh "mvn clean install -f MyWebApp/pom.xml"
            }
        }
        
        stage ("code scan") {
            steps {
                withSonarQubeEnv("SonarQube") {
                 sh "mvn sonar:sonar -f MyWebApp/pom.xml"
                }
            }
        }
        
        stage ("Coverage") {
            steps {
                jacoco()
            }
        }
        
        stage ("WAR upload") {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'MyWebApp', classifier: '', file: 'MyWebApp/target/MyWebApp.war', type: 'war']], credentialsId: 'c6227520-12cc-4954-a7b3-a6186c6a1e89', groupId: 'com.dept.app', nexusUrl: 'ec2-54-173-218-219.compute-1.amazonaws.com:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
            }
        }
        
        stage ("DEV deploy") {
            steps {
                deploy adapters: [tomcat9(credentialsId: '78412160-a977-40fb-97ed-01b0f02221c5', path: '', url: 'http://ec2-3-80-58-182.compute-1.amazonaws.com:8080/')], contextPath: null, war: '**/*.war'
            }
        }
        
        stage ("Slack") {
            steps {
                    slackSend channel: 'feb-2023-weekend-batch', message: 'DEV Deployment was success, please start sanity testing in DEV env..'
            }
        }
}
