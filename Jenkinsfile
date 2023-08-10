pipeline {
    agent any
	
	environment {
        clientRegistry = "ptanmay0071/onlinemart-client"
        booksRegistry = "ptanmay0071/onlinemart-book"
        mainRegistry = "ptanmay0071/onlinemart-main"
        registryCredential = 'dockerhub'
        
    }
    
     stages{
        stage('fetch'){
            steps{
                git branch: 'main' , url:'https://github.com/ptanmay0071/onlinemart-cicd.git'
            }
        }
        
        stage('build client'){
            steps{
                script {
                    dockerImage = docker.build( clientRegistry + ":$BUILD_NUMBER", "./client/")
                 }
            }
        }
        stage('Deploy Image client') {
            steps{
              script {
                docker.withRegistry( '', registryCredential ) {
                  dockerImage.push("$BUILD_NUMBER")
                  dockerImage.push('latest')
                }
              }
            }
          }
          stage('Kubernetes Deploy client') {
            agent { label 'KOPS' }
            steps {
                  
                    sh """
                      helm upgrade onlinemart onlinemartcharts --install --set "onlinemartcharts-frontend.image.client.tag=${BUILD_NUMBER}" --namespace prod
                      """
                  
                 }  
        }
          stage('build books'){
            steps{
                script {
                    dockerImage2 = docker.build( booksRegistry + ":$BUILD_NUMBER", "./javaapi/")
                 }
            }
        }
        stage('Deploy Image books') {
            steps{
              script {
                docker.withRegistry( '', registryCredential ) {
                  dockerImage2.push("$BUILD_NUMBER")
                  dockerImage2.push('latest')
                }
              }
            }
          }
          stage('Kubernetes Deploy books ') {
            agent { label 'KOPS' }
            steps {
                  
                    sh """
                      helm upgrade onlinemart onlinemartcharts --install --set "onlinemartcharts-backend.image.books.tag=${BUILD_NUMBER}" --namespace prod
                      """
                  
                 }  
        }
          stage('build main'){
            steps{
                script {
                    dockerImage3 = docker.build( mainRegistry + ":$BUILD_NUMBER", "./nodeapi/")
                 }
            }
        }
        stage('Deploy Image main') {
            steps{
              script {
                docker.withRegistry( '', registryCredential ) {
                  dockerImage3.push("$BUILD_NUMBER")
                  dockerImage3.push('latest')
                }
              }
            }
          }
          stage('Kubernetes Deploy main') {
            agent { label 'KOPS' }
            steps {
                  
                    sh """
                      helm upgrade onlinemart onlinemartcharts --install --set "onlinemartcharts-backend.image.main.tag=${BUILD_NUMBER}" --namespace prod
                      """
                  
                 }
          }  
        }
          
    }
}
