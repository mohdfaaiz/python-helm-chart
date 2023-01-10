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
               
                  // some block
                    sh 'docker login -u mohammedfaaiz -p ${dockerhubpwd}'
                    
                    sh 'docker push mohammedfaaiz/helm:latest'
                    }
       }
        // stage('add build number'){
        //     steps{
        //         sh 'sed -i "s/repository: .*/repository: "mohammedfaaiz\/helm": /g" flaskchart/values.yaml'
        //         sh 'sed -i "s/tag: .*/tag: "1.1"/g" flaskchart/values.yaml '
        //     }
        // }

        stage('AWS ECR login') {
            steps {
                script {
                   
                        sh 'aws ecr get-login-password  --region us-east-1 | helm registry login --username AWS  --password-stdin 825943142547.dkr.ecr.us-east-1.amazonaws.com'
   
                  
                    

                }
            }
        }
           stage("compress file for exporting"){
            steps{
                sh 'tar cvzf helm-test-chart.${BUILD_NUMBER}.tgz flaskchart '
                // sh 'docker tag nginx-ingress:latest 976846671615.dkr.ecr.us-east-1.amazonaws.com/nginx-ingress:latest'
            }
        }
        // stage('Docker image push'){
        //     steps{
        //         script{
        //             sh 'docker tag flaskchart:latest 825943142547.dkr.ecr.us-east-1.amazonaws.com/flaskchart:latest'
        //             sh 'docker push 825943142547.dkr.ecr.us-east-1.amazonaws.com/helm-test-chart:latest'
        //         }
        //     }
        // }
     
        stage('AWS ECR push') {
            steps {
                script {
                    // sh 'docker tag fayizv/flask:latest 707032823801.dkr.ecr.us-east-1.amazonaws.com/flask-deploy:${BUILD_NUMBER}' 
                    // sh 'docker push 707032823801.dkr.ecr.us-east-1.amazonaws.com/flask-deploy:${BUILD_NUMBER}'
                    sh 'helm push helm-test-chart.${BUILD_NUMBER}.tgz oci://825943142547.dkr.ecr.us-east-1.amazonaws.com'

                }
            }
        }

//         stage('Helm Push to ECR') {
//             steps {
//                 sh 'echo version : 0.${BUILD_NUMBER}.0 >> flaskchart/Chart.yaml'
//                 sh 'helm package flaskchart'
// //                 sh 'tar cvzf flask-deploy.0.${BUILD_NUMBER}.0.tgz flaskchart '
//                 sh 'aws ecr get-login-password  --region us-east-1 | helm registry login --username AWS  --password-stdin 707032823801.dkr.ecr.us-east-1.amazonaws.com'
//                 sh 'helm push flaskchart-0.${BUILD_NUMBER}.0.tgz oci://707032823801.dkr.ecr.us-east-1.amazonaws.com/'
//                 sh 'rm -rf flaskchart-*'
//                 }
//         }
//         stage('Invoke Build number to Pipeline Deployjob') {
//             steps {
//                 build job: 'DeployJob', parameters : [[ $class: 'StringParameterValue', name: 'buildnum', value: "${BUILD_NUMBER}"]]
//             }
//         }
       
    }
}
