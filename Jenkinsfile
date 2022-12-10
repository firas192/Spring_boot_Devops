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
    stage('Docker build')
        {
            steps {
                 sh 'docker build --build-arg IP=0.0.0.0 -t hajersaidi/achatback  .'
            }
        }
        stage('Docker login')
        {
            steps {
                sh 'echo $dockerhub_PSW | docker login -u hajersaidi -p dckr_pat_1XTH7dQVwz_yuf0KrwuymdQvRJU'
            }    
       
        }
      stage('Push') {

			steps {
				sh 'docker push hajersaidi/achatback'
			}
		}
        
       stage('Run app With DockerCompose') {
              steps {
                  sh "docker-compose -f docker-compose.yml up -d  "
              }
              }
       stage('Email notification'){
           steps {
            mail bcc: '', body: '''Hello Hajer, It's Jenkins,
            Your Devops Pipeline is succeeded.
            Best Regards''', cc: '', from: '', replyTo: '', subject: 'Devops Pipeline', to: 'hajer.saidi@esprit.tn'
            }
       }
  }
}
