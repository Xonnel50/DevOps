pipeline {
    agent any
    environment {
        Docker_Image_Name = 'myimage'
        Docker_Tag = 'v2'
    }
    
    
    
    stages {
        stage('Pre-Checks') {
        parallel {
        stage('Docker-Verify') {
            steps {
                retry(3) {
                sh 'docker --version'
                }
            }
        }
    
        stage('Git-Verify') {
            steps {
                sh 'git --version'
            }
        }
        }
        }
        
        stage('Docker-Build') {
            steps {
                sh "docker build -t ${Docker_Image_Name}:${env.BUILD_NUMBER} ."
            }
        } 
        stage('Docker-Image-Verify') {
            steps {
                sh 'docker images'
            }
        } 
    }
}
