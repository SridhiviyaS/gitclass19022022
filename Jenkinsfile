pipeline{
    agent any
    tools {
         maven 'maven3'
    }
    environment {
        DOCKER_TAG = get_Version()
    }
    stages{
        stage("SCM Checkout") {
            steps{
            git credentialsId: 'gitcredentials', url: 'https://github.com/SridhiviyaS/dockeransiblejenkins'
            }
        }
        
        stage("Maven Build") {
            steps{
               sh 'mvn clean package' 
            }   
        }
        
        stage("Docker Build") {
            steps{
                sh "docker build . -t greensclass/greensapp:${DOCKER_TAG}"
            }
        }
        
        stage("Docker Push"){
            steps{
            withCredentials([string(credentialsId: 'dockerhubPass', variable: 'dockerpass')]) {
                 sh "docker login -u greensclass -p ${dockerpass} "
                }
            
            sh "docker push greensclass/greensapp:${DOCKER_TAG}"
        }
        }
        stage("Docker Deploy"){
            steps{
                withCredentials([string(credentialsId: 'dockerhubPass', variable: 'dockerpass')]) {
                 ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'Ansible', inventory: 'dev.inv', playbook: 'sample.yml'
                }
                
            }
        }
    }
}

def get_Version(){
    def commitHash= sh label: 'commitHash', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
