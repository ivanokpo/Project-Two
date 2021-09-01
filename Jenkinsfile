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
            
     }   
}
