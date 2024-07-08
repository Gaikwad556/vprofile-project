pipeline {
    agent any
    tools {
        jdk "jdk11"
        maven "mvn"
    }
    environment {
        awscreds = "ecr:us-east-2:awscreds"
        regristryurl = "891377177922.dkr.ecr.us-east-2.amazonaws.com/myjenkinsapp"
        vprofileurl = "https://891377177922.dkr.ecr.us-east-2.amazonaws.com"
        dockerred = "dockerhub"
    }
    stages{
        stage("fetch code"){
            steps {
                git branch: "docker" , url: "https://github.com/hkhcoder/vprofile-project.git"
            }
        }
        stage("mvn test") {
            steps {
                sh "mvn test"
            }
        }
        stage("mvn checkstyle"){
            steps{
                sh "mvn checkstyle:checkstyle"
            }
        }
        stage("Quality analysis"){
            environment {
                scannerHome = tool "sonar4"
            }
            steps{
                withSonarQubeEnv('sonarqube') { // If you have configured more than one global server connection, you can specify its name
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile-project \
                    -Dsonar.projectName=vpro-repo \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec \
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

        stage("Build Image") {
            steps {
                script {
                    dockerImage = docker.build(regristryurl + ":${BUILD_NUMBER}", "./Docker-files/app/multistage/")
                }
            }
        }
        stage("Upload Image"){
            steps {
                script {
                    docker.withRegistry(vprofileurl , awscreds){
                        dockerImage.push("${BUILD_NUMBER}")
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage("build docker image"){
            steps{
                script{
                    dockerImage = docker.build("ssgaikwad/botss" + ":${BUILD_NUMBER}" , "./Docker-files/app/multistage/")
                }
            }
        }
        stage("upload docker image "){
            steps{
                script{
                    docker.withRegistry('',dockerred){
                        dockerImage.push(":${BUILD_NUMBER}")
                        dockerImage.push("latest")
                    }
                }
            }
        }
        
    }
}