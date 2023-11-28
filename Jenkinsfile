pipeline {
    agent any
    environment {
        dockerhub_user = "28cloud"
        IMAGE_TAG = "${BUILD_NUMBER}"
        APP_NAME = "gitops-argo-app"
        IMAGE_NAME = "${dockerhub_user}" + "/" "${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
    }
    stages {
        stage('clean workspace'){
            steps {
                script{
                    cleanWs()
                }
            }
        }
        stage('git checkout'){
            steps{
                script{
                    git credentials : 'github',
                    url: 'https://github.com/RAM28EC/GitOps-Argo-ci.git',
                    branch: 'main'
                }
            }
        }
        stage('build docker image'){
            steps{
                script{
                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('push docker image'){
            steps{
                script{
                    docker.withRegistry('', REGISTRY_CREDS){
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push('latest')
                    }
                }
            }
        }
        stage('Delete docker image'){
            steps{
                script{
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}" 
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
        
        stage('trigger change pipeline'){
            steps{
                script{
                    sh "curl -v -k -user admin: 118f44c8a7332338aef23a8d002dd99e68 -X POST -H 'cache-control:no-cache' -H 'connect:type:application/x-www-form-urlencoded' -data 'IMAGE_TAG=${IMAGE_TAG}' 'http://3.110.185.31:8080/job/gitops-argo-cd/buildWithParameters?token=gitops-config'"
                }
            }
        }

    }
}