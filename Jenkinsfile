pipeline {
	agent any
	environment {
		username="wizard503"
		registry="wizard503/baches"
		dockerImage=''
	}
	stages {
		stage('Build') {
			steps {
				echo 'Building..'
			}
		}
		stage('Test') {
			steps {
				sh 'mvn -f pom.xml clean test'
			}
		}
		stage('Building Image') {
			steps {
				withCredentials([string(credentialsId: 'dockerhub_id', variable: 'dockerhub_id')]) {
					sh 'chmod 666 /var/run/docker.sock'
					sh 'docker login -u ${username} --password-stdin ${dockerhub_id}'
				}
				script {
					dockerImage=docker.build registry
				}
			}
		}
		stage('Upload Image') {
			steps{    
				script {
					docker.withRegistry( '', registryCredential )
					dockerImage.push()
				}
			}
		}
		stage('docker stop container') {
			steps {
				sh 'docker ps -f name=bachesContainer -q | xargs --no-run-if-empty docker container stop'
				sh 'docker container ls -a -fname=bachesContainer -q | xargs -r docker container rm'
			}
		}
		stage('Docker Run') {
			steps{
				script {
					dockerImage.run("-p 9090:8080 --add-host db:192.168.1.47 --rm --name bachesContainer")
				}
			}
		}
	}
}
