pipeline {
    agent none

    stages {
                
        stage("mvn build") {
            agent {
                docker { 
                    image 'maven:3.8.1-adoptopenjdk-8' 
                }
            }
            steps {
                sh 'mvn clean install'
                stash includes: 'webapp/**/*.war', name: 'webappwar'
            }
        }

        stage("Docker Build") {
            agent any
            steps {
                unstash 'webappwar'
                sh "cp webapp/target/webapp.war ."
                sh "docker build -t simple-mvn:${env.BUILD_NUMBER} ."

                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockercred') { 
                        def dkrImageDev = docker.build("sambitpritamnayak/simple-mvn-dev:${env.BUILD_NUMBER}")
                        def dkrImageProd = docker.build("sambitpritamnayak/simple-mvn-prod:${env.BUILD_NUMBER}")
                        dkrImageDev.push()
                        dkrImageProd.push()
                    }    
                }
            }
        }
        
        stage("Dev Deploy") {
            steps {
                echo 'dev deploy'
                sh "helm upgrade --install --set image.tag=${env.BUILD_NUMBER} --values values-dev.yaml"
            }
        }
        
        stage("Prod Deploy") {
            steps {
                echo 'prod deploy'
                sh "helm upgrade --install --set image.tag=${env.BUILD_NUMBER} --values values-prod.yaml"
            }
        }
    }
}