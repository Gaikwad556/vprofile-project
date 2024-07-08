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
    }
}