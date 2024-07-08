pipeline {
    agent any
    tools {
        jdk "jdk11"
        maven "mvn"
    }
    stages {
        stage("mvn test"){
            steps{
                sh "mvn test"
            }
        }
        stage("mvn checkstyle"){
            steps {
                sh "mvn checkstyle:checkstyle"
            }
        }
        stage('SonarQube analysis') {
            environment {
                scannerHome = tool "sonar4"
            }
            steps{
                    withSonarQubeEnv('sonarqube') { // If you have configured more than one global server connection, you can specify its name
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile-project \
                    -Dsonar.projectName=vpro-repo \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=src/ \
                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec/ \
                    -Dsonar.java.checkstyle.reportsPath=target/checkstyle-result.xml'''
                }
                
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}