pipeline {
    agent any
  tools {
  maven 'maven-3.9.3'
   }
   environment {
      DOCKER_TAG = getVersion()
    }

    stages {
        stage('CLEAN WORKSPACE') {
            steps {
                cleanWs()
            }
        }

        stage('CHECKOUT CODE') {
            steps {
                git 'https://github.com/Praveeen1996/myweb.git'
            }
        }
        stage('BUILD-TOOL') {
            steps {
                 sh 'mvn clean install package'
            }
        }
        stage('DOCKER BUILD'){
            steps{
                sh "docker build . -t praveenhema/myweb:${DOCKER_TAG} "
            }
        }
        stage('DOCKERHUB PUSH'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u praveenhema -p ${dockerHubPwd}"
                }
                
                sh "docker push praveenhema/myweb:${DOCKER_TAG} "
            }
        }
       stage('Deploy To K8S'){
            steps{
                script{
                    kubernetesDeploy (configs: 'deployments.yaml',kubeconfigId: 'k8configpwd')
                }
            }
        }  
    }
}
def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}

