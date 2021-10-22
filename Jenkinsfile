pipeline {
    agent any
    environment {
      DOCKER_TAG = getVersion()
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }       
        stage('Docker Build'){
            steps{
                echo "Create docker image gogolevjuri/firstapp:${DOCKER_TAG} "
                sh "docker build . -t gogolevjuri/firstapp:latest -t gogolevjuri/firstapp:${DOCKER_TAG} "
            }
        }
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                    echo 'Logging into docker hub'
                    sh "docker login -u gogolevjuri -p ${dockerHubPwd}"
                }
                echo 'Docker push now'
                sh "docker push gogolevjuri/firstapp --all-tags "
            }
        }
        stage('clear docker images') {
            steps {
                echo 'removing images....'
                sh "docker rmi gogolevjuri/firstapp:${DOCKER_TAG} "
                sh "docker rmi gogolevjuri/firstapp:latest "
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
