pipeline {
	environment {

		registry = "umpr/mavenbuild"
		registryCredential = 'dockerhubCreds'
		dockerImage = ''
	}
	agent any
	stages {
		stage('Complile the project') {
			steps {
				sh 'mvn clean package'
			}
		}
		stage('Building our image') {
			steps {
				script {
					dockerImage = docker.build registry + ":$BUILD_NUMBER"
				}
			}
		}
		stage('Deploy our image') {
			steps {
				script {
					docker.withRegistry('', registryCredential) {
					    dockerImage.push()
					    dockerImage.pull()
					}
				}
			}
		}
		stage('Pull and Run image') {
			steps {
				script {
					docker.withRegistry('', registryCredential) {
						
						dockerImage.run('-d -p 8080:8080 --name mavenbuild ')
					}
				}
			}
		}
	}
}
