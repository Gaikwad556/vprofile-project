pipeline {
    agent any
    tools {
        jdk "jdk11"
        maven "mvn"
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
        
    }
}