pipeline {
    agent any
    environment {
      MAVEN_OPTS="--add-opens java.base/java.lang=ALL-UNNAMED"   
    }    

    stages {   
        stage('Build with maven') {
            steps {
                sh 'cd SampleWebApp && mvn clean install'
            }
        }
        
             stage('Test') {
            steps {
                sh 'cd SampleWebApp && mvn test'
            }
        
            }
        stage('Code Qualty Scan') {

           steps {
                  withSonarQubeEnv('sonar_scanner') {
 

             sh "mvn -f SampleWebApp/pom.xml sonar:sonar"      
               }
            }
       }
        stage('Quality Gate') {
          steps {
                 waitForQualityGate abortPipeline: true
              }
        }
        stage('push to nexus') {
            steps {
              nexusArtifactUploader artifacts: [[artifactId: 'SampleWebApp', classifier: '', file: 'SampleWebApp/target/SampleWebApp.war', type: 'war']], credentialsId: 'nexus_pass', groupId: 'SampleWebApp', nexusUrl: 'ec2-98-84-164-121.compute-1.amazonaws.com:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
        }
        }    
        stage('deploy to tomcat') {
          steps {
             deploy adapters: [tomcat9(credentialsId: 'tom_pass', path: '', url: 'http://3.239.186.2:8080')], contextPath: 'monoliticapp', war: '**/*.war'
                          
              
          }
            
        }
            
        }
    }
