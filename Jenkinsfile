pipeline{
    agent any
    tools {
      maven 'M2_HOME'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
                git url: 'https://github.com/moosic39/MAVEN_WAR_HELLO_WORLD.git',
                credentialsId: 'github', 
                branch: "main"
                    
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build'){
            steps{
                sh "docker build . -t moosic39/alfred-pennyworth:${DOCKER_TAG} "
            }
        }
        
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'moosic39')]) {
                    sh "cat ~/dockerhubPassword | docker login -u moosic39 --password-stdin"
                }
                
                sh "docker push moosic39/alfred-pennyworth:${DOCKER_TAG} "
            }
        }
        
        stage('Docker Deploy'){
            steps{
              ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}