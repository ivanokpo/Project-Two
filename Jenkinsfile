pipeline {
    agent any
    environment{
        // DATABASE_URI = credentials('DB-URI')
        SECRET_KEY = credentials('secretkey')
	DOCKER_LOGIN = credentials('dockerhub')
	SSH_HOSTKEY = credentials('sshhostkey')
    }
    stages {
        stage('Build') {
            steps {
                sh 'docker-compose build'
		sh 'docker system prune -f'
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
             sh 'cat ${SSH_HOSTKEY} > hostkeyfile'
	     sh 'chmod 600 hostkeyfile'
	     sh 'eval `ssh-agent -s`'
	     sh 'scp -i ~/.ssh/managerkeygen ./nginx/nginx.conf jenkins@10.0.2.118:/home/jenkins/nginx/nginx.conf'
	     sh 'scp -i ~/.ssh/managerkeygen docker-compose.yaml jenkins@10.0.2.118:~'
	     sh 'ssh -i ~/.ssh/managerkeygen jenkins@10.0.2.118 docker stack deploy --compose-file docker-compose.yaml project-stack'  
	     sh 'ssh -i ~/.ssh/managerkeygen jenkins@10.0.2.118 "source .profile && echo ${DATABASE_URI}"'  
            }
            
         }   
    }
}
