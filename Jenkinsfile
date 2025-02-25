pipeline {
    
    agent any
    
    tools{
        jdk 'jdk'
        maven 'maven'
    }
    
    stages {
        stage("Code Checkout"){
            steps{
                git branch:'main',url:'https://github.com/vijeshdevops89/Task-Master.git'
            }
        }
        
        stage('Code Build'){
            steps{
                sh 'mvn clean package'
            }
        }
        
        stage('Docker build and tag') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-creds') {
                        sh '''
                        docker build -t task .
                        docker tag task vijesh89/taskmaster:${BUILD_NUMBER}
                        '''
                    }
                }
            }
            
        }
        
        stage('Docker Push'){
            steps{
                script{
                    withDockerRegistry(credentialsId:'docker-creds'){
                        sh'''
                        docker push vijesh89/taskmaster:${BUILD_NUMBER}
                        '''
                    }
                }
            }
        }
        
        stage('Check and Remove Running Container'){
            steps{
                script {
                    def containerExists = sh(script: "docker ps --format '{{.Names}}' | grep -q '^taskapp\$'", returnStatus: true)
                    
                    if (containerExists == 0) {
                        echo "Container 'taskapp' is running. Stopping and removing it..."
                        sh "docker stop taskapp && docker rm taskapp"
                    } else {
                        echo "Container 'taskapp' is not running. Proceeding..."
                    }
                }
            }
        }
        
        stage('Create and Run Container'){
            steps{
                script{
                    withDockerRegistry(credentialsId:'docker-creds'){
                        sh'''
                        echo 'Creating container...'
                        docker run -d --name taskapp -p 8081:8080 vijesh89/taskmaster:${BUILD_NUMBER}
                        '''
                    }
                }
            }
        }
    }
}
