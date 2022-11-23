pipeline {
    agent any

    stages {
        stage('Docker-Verify') {
            steps {
                sh 'docker --version'
            }
        }
    
        stage('Git-Verify') {
            steps {
                sh 'git --version'
            }
        }
        
        stage('Docker-Build') {
            steps {
                sh 'docker build -t myimage:v1 .'
            }
        }    
    }
}
