pipeline {
    
    agent {
        label "linuxbuildnode"
    }
    
    
    stages {
        stage('SCM') {
            steps {
                git 'https://github.com/tripathimanoj/jenkins-f-repo.git'
            }
            
        }
        
        stage('Build by Maven Package') {
            steps{
                sh 'mvn clean package'
            }
        }
        
        stage('Build Docker OWN image') {
            steps {
                sh "sudo docker build -t manojtripathi/javaweb:${BUILD_TAG} ."
            }
        }
        
        stage('Push Image to Docker HUB') {
            steps {
                
                withCredentials([string(credentialsId: 'DOCKER_HUB_PWD', variable: 'DOCKER_HUB_PASS_CODE')]) {
                // some block
                sh "sudo docker login -u manojtripathi -p $DOCKER_HUB_PASS_CODE"
}
                
                sh "sudo docker push manojtripathi/javaweb:${BUILD_TAG}"
            }
        }
        
        stage('Deploy webAPP in DEV Env') {
            steps {
                sh 'sudo docker rm -f myjavaapp'
                sh "sudo docker run -d -p 8080:8080 --name myjavaapp manojtripathi/javaweb:${BUILD_TAG}"
            }
        }
        
        stage('Deploy webAPP in QA/TEST Env') {
            steps {
                sshagent(['AQT-ENV-SSH-CRED']) {
                // some block
                sh "ssh -o StrictHostKeyChecking=no ec2-user@3.110.185.5 sudo docker rm -f myjavaapp"
                sh "ssh ec2-user@3.110.185.5 sudo docker run -d -p 8080:8080 --name myjavaapp manojtripathi/javaweb:${BUILD_TAG}"
                            }
            }
        }
        
        
        
        /*
        stage('QAT  Test') {
            steps {
                sh 'curl --silent http://3.110.185.5:8080/java-web-app/ | grep India '
            }
        }
        */
        
        stage('Approval') {
            steps {
                input(message: "Release to Prod Env ... ?")
            }
        }
        
        stage('Deploy webAPP in Prod Env') {
            steps {
                sshagent(['AQT-ENV-SSH-CRED']) {
                // some block of code
                sh "ssh -o StrictHostKeyChecking=no ec2-user@65.2.35.195 sudo docker rm -f myjavaapp"
                sh "ssh ec2-user@65.2.35.195 sudo docker run -d -p 8080:8080 --name myjavaapp manojtripathi/javaweb:${BUILD_TAG}"
                            }
            }
        }
        
        
    }
}
