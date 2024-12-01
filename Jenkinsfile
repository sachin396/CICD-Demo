pipeline {
	agent any
	tools {
		nodejs 'NodeJS'
	}
	environment {
		SONAR_SCANNER_HOME = tool 'SonarQubeScanner'
		SONAR_PROJECT_KEY = 'CICD1'
		DOCKER_HUB_CREDENTIALS_ID = 'dockerhub-jenkins-token'
		DOCKER_HUB_REPO = '123patil/nodecicd'
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
		stage('SonarQube Analysis'){
			steps {
				withCredentials([string(credentialsId: 'cicd-sonarqube-token', variable: 'SONAR_TOKEN')]) {
    					
					withSonarQubeEnv('SonarQube') {
						sh """
						${SONAR_SCANNER_HOME}/bin/sonar-scanner \
						-Dsonar.projectKey=${SONAR_PROJECT_KEY} \
						-Dsonar.sources=. \
						-Dsonar.host.url=http://3.111.53.230:9000/ \
						-Dsonar.login=${SONAR_TOKEN}
						"""   						
					}
				}
			}
		}
		stage('Build Docker Image'){
			steps {
				script {
					dockerImage = docker.build("${DOCKER_HUB_REPO}:latest")
				}
			}
		}
		stage('Push Image to DockerHub'){
			steps {
				script {
					docker.withRegistry('https://registry.hub.docker.com', "${DOCKER_HUB_CREDENTIALS_ID}"){
						dockerImage.push('latest')
					}
				}
			}
		}
		stage('Install Kubectl'){
			steps {
				sh '''
				curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
                		chmod +x kubectl
                		sudo mv kubectl /usr/local/bin/kubectl
				'''
			}
		}
		stage('Deploy to Kubernetes'){
			steps {
				script {
					kubeconfig(caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCakNDQWU2Z0F3SUJBZ0lCQVRBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwdGFXNXAKYTNWaVpVTkJNQjRYRFRJME1URXpNREV6TURZeU9Gb1hEVE0wTVRFeU9URXpNRFl5T0Zvd0ZURVRNQkVHQTFVRQpBeE1LYldsdWFXdDFZbVZEUVRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTG5XCmllSldReE9NT1p5VnR2Qm9PTXFzSmhQQTBOdWV6YmpUQXpQRzZzcks5TDFoOGVvenh0Nnk3VUJUR0NPTFRacTcKVHFtWFYyckpBMWdMS2xnSW16U1ZFUnBrM2M3d2N5cDZPV1hNMG95Ui8zRHlvOG1tOTlXdmlxbTdFRThJMVdTNgpQd3VDUVhnUytrTzY1WTY5TU9kVjFGNDZ4ek1PektQRDJpeThDaG5nQ3NtaEVmR1pacTJEVUxabGg3YnVlMXZhCkxvYjlQb0ZRSkNiaHd5M3FQam5EeTBxak5YUDgwVk1BZnNnYjBrMTJoS2F5VllmWWxuMG9LYTYxNm9ORVFqMGkKUnlqL2Q4UndDbTFUUXVWcXd1RFJSdS8weWFvR0Z0M0hSSzc2bFM4SWlBUjJIR3pncjEyRk8vY3ZMa2JGZ01MSwpwaWJFYXNwTXREM1ZEQUh6ODE4Q0F3RUFBYU5oTUY4d0RnWURWUjBQQVFIL0JBUURBZ0trTUIwR0ExVWRKUVFXCk1CUUdDQ3NHQVFVRkJ3TUNCZ2dyQmdFRkJRY0RBVEFQQmdOVkhSTUJBZjhFQlRBREFRSC9NQjBHQTFVZERnUVcKQkJTUXBqK3VBRkU3NjRDNXpwM29NOXNKYVNiYWdEQU5CZ2txaGtpRzl3MEJBUXNGQUFPQ0FRRUFZTldQOTA3SAoyVHk5U2U0ZjFBbHZqTVB0Ym1mSklGQWluaUVnTDdBb1ZCV1hUdGVXbXk1ZnliVllxT010cGRhRVlSU2ZUMk15CmxDWVo5UHBiZnJsQmo3YklmYkZaTTIvT25Mb2hCVmQ5RlF2QkNuZXEwQWdGejd4TTNxV2RGV0V0YVhQVnJiT0kKUDM5K3ZiYmdEM1JNQ3VIbTJreTJya1FtMS8vSmxiQnJPYWVIZEY0UitkZFRRNzl0ZGxjS0FvWEFjdnp0N3QvQwpVRmVzalp0U1k3M1R5a0EzWnRHREkrNmVQTmRUZ25vZXRRaUlXbmFjM2FvcXFCSHJTYlpTQjAzNit2bjRpOGEwCkp0azhtOU1zREtVb2drejk0WCt3OE8vMVN6S3lJNDVmZjQxSEpBb2ErMFgzaUhZYVFEcDd1Y09IYlVIK3RubXcKd3ZxSEJNMG1lWUZKdHc9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==', credentialsId: 'kubeconfig', serverUrl: 'https://192.168.49.2:8443') {
    						sh 'kubectl apply -f deployment.yaml'
					}
				}
			}
		}
		
	}
}
