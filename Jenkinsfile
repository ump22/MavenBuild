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
sh(returnStdout: true, script: '''#!/bin/bash
            result=$(sudo docker ps | grep -i "mavenbuild") 
            if [ -n "$result"  ];then
            echo "Stop container"
            docker stop mavenbuild;
            echo "Remove container";
            docker rm mavenbuild;
            else
            echo "Did not find the container"
            fi
        '''.stripIndent())
        
						sh(returnStdout: true, script: '''#!/bin/bash
            imags=$(sudo docker images --filter=reference=umpr/mavenbuild --format "{{.ID}}")
            for img in $imags; do  docker image rm $img -f; done
        '''.stripIndent())
		
                
        
						
						dockerImage.pull()
					}
				}
				
			}
		}
		stage('Pull and Run image') {
			steps {
				script {
					docker.withRegistry('', registryCredential) {

						dockerImage.run('-p 8080:8080 --name mavenbuild ')
					}
				}
			}
		}
	}
}
