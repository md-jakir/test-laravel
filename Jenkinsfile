def appimage
pipeline{
    agent any
    environment {
        SERVICE_NAME = "app2"
        CONTAINER_NAME = "laravel-app2"
        DOCKER_USERNAME= "mdjakir"
        VERSION="1.0.0"
        TAG_NAME="${BUILD_NUMBER}"
        //registry= "mdjakir/${JOB_BASE_NAME}"
        REGISTRY="${DOCKER_USERNAME}/${JOB_BASE_NAME}"
        BRANCH_NAME="master"
        GIT_REPO="https://github.com/md-jakir/test-laravel.git"
    }
	
    stages{
        stage('SCM'){
            steps{
                git branch: "${env.BRANCH_NAME}",url: "${env.GIT_REPO}"
            }
        }
        
        stage('Build'){
            /*environment{
                DOCKER_USERNAME = 'devopsibcs'
            }*/
            steps{
                script {
                    //appimage = docker.build("$env.registry")}
                    sh 'docker compose build --no-cache'
                    //sh 'docker compose build'
                    sh 'docker image tag ${JOB_BASE_NAME} ${REGISTRY}:${TAG_NAME}'}
            }
        }
        
        stage('Push'){
            environment {
               registryCredential = 'DOCKER-HUB'
           }
           
            steps{
                script {
                   docker.withRegistry( '', "$env.registryCredential" ) {
                       //appimage.push("$TAG_NAME")
                       sh 'docker push ${REGISTRY}:${TAG_NAME}'
                   }
               }
            }
        }
        
        stage('Deploy') {
            steps{
                kubernetesDeploy configs: 'deploy/app2-deploy.yml', enableConfigSubstitution: true, kubeconfigId: 'KUBE-CONFIG'
            }
        }
        
        stage('Cleaning up') { 

			steps {
				sh "docker rmi $env.registry:$TAG_NAME"
			}
        }
    }
    
    post { 
        always { 
            cleanWs()
        }
    }
}
