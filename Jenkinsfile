pipeline{
    agent any  
    stages {
        stage("Checkout") {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/mohdfaaiz/python-helm-chart']]])
            }
        }  

       stage("Build Docker Image") {
            steps {
                script {
                    sh 'docker build -t mohammedfaaiz/helm .'
                    sh 'docker tag mohammedfaaiz/helm mohammedfaaiz/helm:${BUILD_NUMBER} '
                }               
            }
        }        

       stage('pushing to dockerhub') {
            steps {              
                    sh 'docker login -u mohammedfaaiz -p ${dockerhubpwd}'
                    
                    sh 'docker push mohammedfaaiz/helm:latest'
                    }
       }
        stage('AWS ECR login') {
            steps {
                script {
                        sh 'aws ecr get-login-password  --region us-east-1 | helm registry login --username AWS  --password-stdin 825943142547.dkr.ecr.us-east-1.amazonaws.com'
                }
            }
        }
        stage('AWS ECR push') {
            steps {
                script {
                    sh 'echo version : 0.${BUILD_NUMBER}.0 >> flaskchart/Chart.yaml'
                    sh 'helm package flaskchart'
                    sh 'helm push helm-test-chart-0.${BUILD_NUMBER}.0.tgz oci://825943142547.dkr.ecr.us-east-1.amazonaws.com'

                }
            }
        }
        stage('Invoke Build number to Pipeline Deployjob') {
            steps {
                build job: 'DeployJob', parameters : [[ $class: 'StringParameterValue', name: 'buildnum', value: "${BUILD_NUMBER}"]]
            }
        }
       
    }
}
