pipeline {
    agent any
    tools{
        maven 'maven3'
    }
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Pavangowda73/Blogging-APP.git'
            }
        }
        stage('maven compile'){
            steps{
                sh "mvn compile"
            }
        }
        stage('maven test'){
            steps{
                sh "mvn test"
            }
        }
        stage('trivy file system scan'){
            steps{
                sh "trivy fs --format table -o fs.html ."
            }
        }
        stage('SonarQube Analysis'){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=blogging-app -Dsonar.projectKey=blogging-app \
                          -Dsonar.java.binaries=target'''
                }
            }
        }
        stage('maven package'){
            steps{
                sh "mvn package"
            }
        }
        stage('docker build and tag'){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh "docker build -t pavan73384/bloggingapp:latest ."
                    }
                }
            }
        }
        stage('trviy dcoker image scan'){
            steps{
                sh "trivy image --format table -o image.html pavan73384/bloggingapp:latest"
            }
        }
        stage('docker image push'){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh "docker push pavan73384/bloggingapp:latest"
                    }
                }
            }
        }
        
    }
}
