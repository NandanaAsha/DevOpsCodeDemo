pipeline {
    tools { 
        maven 'mymaven'
    }
    agent any
    stages{
        stage ('Clone repo') {
            steps{
              git 'https://github.com/NandanaAsha/DevOpsCodeDemo.git'  
            }
        }
        stage ('Code review') {
            steps{
                sh 'mvn pmd:pmd'
            }
        }
        stage ('Clean target directory') {
            steps{
                sh 'mvn clean'
            }
        }
        stage ('Packge code into an artifact') {
            steps{
                sh 'mvn package'
            }
        }
        stage ('Copy artifact to workspace') {
            steps{
                sh 'cp /var/lib/jenkins/workspace/Javaapplndeploy/target/addressbook.war .'
            }	
        }
        stage ('Build Custom image') {
            steps{
                sh 'docker build -t myappimg:V$BUILD_NUMBER .'
            }	
        }
        stage ('Run Custom image and create containers') {
            steps{
                sh 'docker run -d --name myappcont_$BUILD_NUMBER -P myappimg:V$BUILD_NUMBER'
            }	
        }
        stage('Push Image to Dockerhub')
         {
            steps{
                withCredentials([string(credentialsId: 'DOCKER_HUB_PASWD', variable: 'DOCKER_HUB_PASWD')]) 
                {
                sh 'docker login -u nandanaasha -p ${DOCKER_HUB_PASWD}'
                }
              
                sh 'docker tag myappimg:V$BUILD_NUMBER nandanaasha/myaddressbook:V1'
                sh 'docker push nandanaasha/myaddressbook:V1'
            }
        }

    }
    
    post {
        always {
            // Archive the build artifacts
            archiveArtifacts artifacts: '**/target/*.war', allowEmptyArchive: true
            
            // Publish JUnit test results
            junit '**/target/surefire-reports/*.xml'
        }
        success {
            echo 'Build and deployment to a container was successful!'
        }
        failure {
            echo 'Build or deployment to container failed!'
        }
    }
}
