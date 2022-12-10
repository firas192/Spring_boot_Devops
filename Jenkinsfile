pipeline {
   agent any
  stages {
     
            stage('Get Project from Github') {
            steps {
                   echo 'Getting Project from Git' 
                git branch: 'main', url: 'https://github.com/firas192/Spring_boot_Devops.git',
                credentialsId:'azaz'
                
            }
}
    
         stage("MVN Clean Install   "){
            steps {
                sh """mvn clean install -Dskiptest """
               /*
               sh " mvn clean  -ptest "
               sh " mvn clean package  -pprod"
                */
            }
        }
	  
   	stage("Junit/Mockito"){
            steps {
                sh """mvn test """
                
            }
        }
    
      stage("Sonar") {
        steps {

       sh "mvn clean verify  sonar:sonar \
     	-Dsonar.projectKey=achat \
 	-Dsonar.host.url=http:192.168.0.13:9000 \
  	-Dsonar.login=76491b39395ca4141d18128a3954e2288a650137 \
        -Dsonar.java.binaries=src/main "
  
  
               }
     }
     
     stage("nexus") {
        steps{
           echo "deploy project on nexus"
           sh 'mvn deploy:deploy-file -DgroupId=tn.esprit.rh -DartifactId=achat -Dversion=1.0 -DgeneratePom=true -Dpackaging=jar -DrepositoryId=deploymentRepo -Durl=http://192.168.0.13:8081/repository/maven-releases/  -Dfile=target/achat-1.0.jar'
        }
     }
     
     /*-----------------*/
    stage("Build docker image") {
        steps{
		
		
           sh ' docker build -t otail/tp_achat_project-1.0 .'
        }
        } 
     
          stage("Deploy image to docker hub") {
        steps{
         	sh 'docker login -uotail -p otailotail8'
            	sh  'docker push otail/tp_achat_project-1.0:latest'
              }
		  
        }
	  
         stage('DOCKER COMPOSE') {
                 steps {
		     
                      sh 'docker-compose up -d'
                   }
              }
	    stage("Deploy  to nexus") {
       steps{
		sh ' docker build -t 192.168.1.21:8082/otail/tp_achat_project-1.0 .'
         	sh 'docker login -u admin -p nexus 192.168.1.21:8082'
            	sh  'docker push 192.168.1.21:8082/otail/tp_achat_project-1.0:latest'
              }
        }
	 /* stage('Promethious') {
                 steps {
		     
                      sh 'docker run -d --name prometheus -p 9091:9091 prom/prometheus'
                   }
                 this doesn't work
              }*/
              
   

  }
  post {
    always {
	     
      cleanWs()  
	    
	  
    }

  }
}
