pipeline {
	agent any
	tools {
		nodejs 'NodeJS'
	}	
	stages {
		stage('GitHub'){
			steps {
				git branch: 'main', credentialsId: 'jen-git-min', url: 'https://github.com/sachin396/CICD-Demo.git'
			}
		}
		stage('Unit Tests'){
			steps {
				sh 'npm test'
				sh 'npm install'
			}

		}
		
	}
}
