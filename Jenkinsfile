node{
      
      stage('SCM Checkout'){
         git 'https://github.com/supnaik/Java-Demo-Application'
      }
      
      stage('Build'){
         // Get maven home path and build
         def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'   
         sh "${mvnHome}/bin/mvn package -Dmaven.test.skip=true"
      }       
       
      stage ('Test'){
         def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'    
         sh "${mvnHome}/bin/mvn verify; sleep 3"
      }
      
      stage('Build Docker Image'){
         sh 'docker build -t supnaik/javademoapp_$JOB_NAME:$BUILD_NUMBER .'
      }  
   
      stage('Publish Docker Image'){
         withCredentials([string(credentialsId: 'dockerpwdsupreet', variable: 'dockerPWD')]) {
              sh "docker login -u supnaik -p ${dockerPWD}"
         }
        sh 'docker push supnaik/javademoapp_$JOB_NAME:$BUILD_NUMBER'
        sh "sed -i.bak 's/#BUILD-NUMBER#/$BUILD_NUMBER/' deployment.yaml"
        sh "sed -i.bak 's/#JOB-NAME#/$JOB_NAME/' deployment.yaml"
      }
          
    
      // ********* For Azure Cluster**************************
      stage('Deploy'){
         def k8Apply= "kubectl apply -f deployment.yaml" 
         withCredentials([string(credentialsId: 'k8pwdsupreet', variable: 'k8PWD')]) {
          sh "sshpass -p ${k8PWD} ssh -o StrictHostKeyChecking=no ubuntu@104.211.154.236" 
          sh "sshpass -p ${k8PWD} scp -r deployment.yaml ubuntu@104.211.14.161:/home/ubuntu" 
          sh "sshpass -p ${k8PWD} ssh  -o StrictHostKeyChecking=no ubuntu@104.211.14.161 ${k8Apply}"
         }
       }
      
         
       
    
       /*
      // ********* For AWS Cluster**************************
      stage('Deploy'){
         def k8Apply= "kubectl apply -f deployment.yaml" 
         withCredentials([string(credentialsId: 'k8pwdsupreet', variable: 'k8PWD')]) {
             sh "sshpass -p ${k8PWD} ssh -o StrictHostKeyChecking=no devops@54.196.52.131"  
             sh "sshpass -p ${k8PWD} scp -r deployment.yaml devops@54.196.52.131:/home/devops" 
             sh "sshpass -p ${k8PWD} ssh -o StrictHostKeyChecking=no devops@54.196.52.131 ${k8Apply}"
         }
       }
        */
  }
