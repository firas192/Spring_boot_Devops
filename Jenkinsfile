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
  	-Dsonar.login=1da96b778d0fd9c1a2476fa22159b79780b283d7 \
        -Dsonar.java.binaries=src/main "
  
  
               }
     }
     
     stage("nexus") {
        steps{
            sh """mvn deploy """
        }
     }
     
     /*-----------------*/
    stage('Docker build')
        {
            steps {
                 sh 'docker build --build-arg IP=0.0.0.0 -t firasgb/achatback  .'
            }
        }
        stage('Docker login')
        {
            steps {
                sh 'echo $dockerhub_PSW | docker login -u firasgb -p dckr_pat_5ut3Pubs5oFhDc27onRKUl_9ZuQ'
            }    
       
        }
      stage('Push') {

			steps {
				sh 'docker push firasgb/achatback'
			}
		}
        
       stage('Run app With DockerCompose') {
              steps {
                  sh "docker-compose -f docker-compose.yml up -d  "
              }
              }
       stage('Email notification'){
           steps {
            mail bcc: '', body: '''Hello Firas, It's Jenkins,
            Your Devops Pipeline is succeeded.
            Best Regards''', cc: '', from: '', replyTo: '', subject: 'Devops Pipeline', to: 'firas.ghobber@esprit.tn'
            }
       }
  }
}
