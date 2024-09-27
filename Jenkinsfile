pipeline {
    agent any 
    
    environment{
        cred = credentials('aws-key')
        dockerhub_cred = credentials('docker-cred')
        DOCKER_IMAGE = "hbayraktar/petclinic"
        DOCKER_TAG = "$BUILD_NUMBER"
    }
    stages{
        
        stage("Git Checkout"){
            steps{
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/hakanbayraktar/Petclinic.git'
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
         stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }
                
         stage("Build"){
            steps{
                sh " mvn clean install"
            }
        }
        
        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        
                        sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                        sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG} "
                    }
                }
            }
        }
        
        
        stage("Deploy To Tomcat"){
            steps{
                sh 'aws eks update-kubeconfig --region us-east-1 --name my-eks-cluste'
                sh 'kubectl apply -f manifest/deployment.yaml'
            }
        }
    }
    post {
        always {
            echo "Job is completed"
        }
        success {
            echo "It is a success"
        }
        failure {
            echo "Job is failed"
        }
    }
}
