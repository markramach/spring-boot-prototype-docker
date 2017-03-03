pipeline {
    agent any
    tools {
        maven 'maven' 
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Sonar Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                  sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar'
                }
            }
        }
    }
}
