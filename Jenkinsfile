pipeline {
    agent any
    stages {
        stage('Git checkout') {
            steps {
               script{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '3ccf848c-37f0-4702-80c0-d7c0611ae978', url: 'https://github.com/shekutest/kubernetes-automation.git']])
                    }
               }
           }   
          stage('Maven Build') {
             steps {
                sh 'mvn clean install'
            }   
        }
       stage ('Docker Build Stage') {
            steps {
                script {
                    sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID sheku786/$JOB_NAME:v1.$BUILD_ID'
                }
            }
        }
        stage ('push docker image to  dockerhub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhub-psd', variable: 'dockerpwd')]) {
                        sh 'docker login -u sheku786 -p ${dockerpwd}'                        
                        sh 'docker image push sheku786/$JOB_NAME:v1.$BUILD_ID'
                    }
                }
            }
        }
       stage ('Deploy docker image on EC2') {
            steps {
                    sh 'docker run -d -p 9090:8080 --name k8s-container sheku786/$JOB_NAME:v1.$BUILD_ID'
            }
       }    
    }
 }
