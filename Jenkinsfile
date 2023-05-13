pipeline {   
  agent{      
    node { label 'docker'}     
  }  
  environment {     
    DOCKERHUB_CREDENTIALS= credentials('docker_hub-aviazo')     
  } 

  triggers {
            pollSCM '* * * * *'
           }   
  stages {  

    stage('Scan') {
      steps {
        withSonarQubeEnv(installationName: 'sq1') { 
          sh './mvnw clean org.sonarsource.scanner.maven:sonar-maven-plugin:3.9.0.2155:sonar'
        }
      }
    }

    stage("Quality Gate") {
      steps {
        timeout(time: 2, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
      }
    }

    stage("Git Checkout"){           
      steps{                
	          git branch: 'dev', url: 'https://github.com/aviazo/hello-world-war.git'             
	          echo 'Git Checkout Completed'            
           }        
    }
    
    stage('Build Docker Image') {         
      steps{                
	          sh 'sudo docker build -t aviazo/hello-world-war_mvn:$BUILD_NUMBER .'           
            echo 'Build Image Completed'                
           }           
    }

    stage('Login to Docker Hub') {         
      steps{                            
	          sh 'echo $DOCKERHUB_CREDENTIALS_PSW | sudo docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'                 
	          echo 'Login Completed'                
           }           
    }
        
    stage('Push Image to Docker Hub') {         
      steps{                            
	          sh 'sudo docker push aviazo/hello-world-war_mvn:$BUILD_NUMBER'                 
            echo 'Push Image Completed'       
           }           
    }      
  } //stages 
  
  post{
    always {  
      sh 'docker logout'           
    }      
  }  
} //pipeline
