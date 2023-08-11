  pipeline{
    agent any

        environment {
        clientRegistry= "chinmaypatilcp7/onlinemart-client"
        bookRegistry= "chinmaypatilcp7/onlinemart-book"
        mainRegistry= "chinmaypatilcp7/onlinemart-main"
        registryCredential= "docker"

    }
    stages{
        stage('fetch code'){
            steps{
                git branch: 'main' , url:''
            }
        }
        stage('build image -client'){
            steps{
                script{
                   dockerimage = docker.build( clientRegistry + ":$BUILD_NUMBER", "./client/")   
                }
            }
        }
        stage('deploy image'){
            steps{
                script{
                    docker.withRegistry('', registryCredential){
                        dockerimage.push("$BUILD_NUMBER")
                        dockerimage.push('latest')
                    }
                }
            }
        }
        stage('kubernets deploy client'){
            agent { label 'KOPS' }
            steps{
                sh """
                  helm upgrade onlinemart martcharts --install --set "martcharts-frontend.image.client.tag={$BUILD_NUMBER}" --namespace mart
                  """
            }

        }
        stage('build image-book'){
            steps{
                script{
                    dockerimage=docker.build( bookRegistry + ":$BUILD_NUMBER", "./javaapi/")
                }
            }
        }
        stage('deploy image-book'){
            steps{
                script{
                    docker.withRegistry('', registryCredential){
                        dockerimage.push("$BUILD_NUMBER")
                        dockerimage.push('latest')
                    }
                }
            }
        }
        stage('kubernets deploy javapi'){
            agent { label 'KOPS' }
            steps{
                sh """
                  helm upgrade onlinemart martcharts --install --set "martcharts-frontend.image.client.tag={$BUILD_NUMBER}" --namespace mart
                  """
            }

        }
        

        stage('build image node'){
            steps{
                script{
                    dockerimage = docker.build( mainRegistry + ":$BUILD_NUMBER", "./nodeapi")
                }
            }
        }
        stage('deploy image nodeapi'){
            steps{
                script{
                    docker.withRegistry('', registryCredential){
                        dockerimage.push("$BUILD_NUMBER")
                        dockerimage.push('latest')
                    }
                }
            }
        }
        stage('kubernets deploy nodeapi'){
            agent { label 'KOPS' }
            steps{
                sh """
                  helm upgrade onlinemart martcharts --install --set "martcharts-backend.image.main.tag={$BUILD_NUMBER}" --namespace mart
                  """
            }
        }

    }
}
