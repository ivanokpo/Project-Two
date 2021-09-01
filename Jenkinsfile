pipeline {
    agent any
    environment{
        DATABASE_URI = credentials('DB-URI')
        SECRET_KEY = credentials('secretkey')
	DOCKER_LOGIN = credentials('dockerhub')
    }
    stages {
        stage('Build') {
            steps {
                sh 'docker-compose build'
            }
        }
	stage('Test'){
	    steps {
	      dir("frontend"){
	      	sh 'pip3 install -r requirements.txt'
	      	sh 'python3 -m pytest --cov application --cov-report html'
	      }
	      dir("backend"){
	      	sh 'pip3 install -r requirements.txt'
              	sh 'python3 -m pytest --cov application --cov-report html'
	      }
	   }
	}

	stage('Artifacts'){
	    steps {
		sh 'docker login -u sjknapp -p ${DOCKER_LOGIN}'
		sh 'docker push sjknapp/project-backend-image:latest'
		sh 'docker push sjknapp/project-frontend-image:latest'
	    }	
        }
  
        stage('Deploy') {
            steps {
             sh 'scp docker-compose.yaml jenkins@10.0.2.118:~'
	     sh 'ssh jenkins@10.0.2.118 docker stack deploy --compose-file docker-compose.yaml project-stack'  
            }
            
         }   
    }
}
