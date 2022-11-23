pipeline {
    agent any
    environment {
        Docker_Image_Name = 'myimage'
        Docker_Tag = 'v2'
    }
    
    options { 
    timestamps() 
    buildDiscarder(logRotator(numToKeepStr: '10'))
    disableConcurrentBuilds()
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
            when {
           expression {     
                  return env.GIT_BRANCH == "origin/main"   
                               }
       }
            steps {
                
                sh "aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 467290638204.dkr.ecr.us-west-2.amazonaws.com"
                sh "docker build -t my-jenkins-project ."
                sh "docker tag my-jenkins-project:latest 467290638204.dkr.ecr.us-west-2.amazonaws.com/my-jenkins-project:latest"
                sh "docker push 467290638204.dkr.ecr.us-west-2.amazonaws.com/my-jenkins-project:latest"
            }
        } 
        stage('Docker-Image-Verify') {
            steps {
                sh 'docker images'
            }
        } 
        stage('Docker-CleanUp') {
            steps {
                sh 'docker rm -f \$(docker ps -a -q) 2> /dev/null || true'
            }
        } 
        
        stage('Docker-Deploy') {
            input
            {
                message "Do you want to proceed for deployment ?"
            }
            steps {
                sh "docker run -itd  -p 80:80 ${Docker_Image_Name}:${env.BUILD_NUMBER}"
                sh "docker ps"
            }
        } 
        
    }
    post {
        always {
            sh 'docker images'        
        }  
        aborted {
            sh 'docker ps'        
        }
        failure {
            sh 'docker rm -f \$(docker ps -a -q) 2> /dev/null || true'        
        }
        success {
            sh 'curl localhost'         
        }
        cleanup {
            sh 'docker image prune -af'   
        }
    }
}
