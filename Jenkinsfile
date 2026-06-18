pipeline {
    agent any

    environment {
        PATH = "/opt/maven/bin:${env.PATH}"
    }

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean deploy'
            }
        }

        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'sonarqube-scanner'


            steps {
                withSonarQubeEnv('sonarqube-connect') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

    }
}

