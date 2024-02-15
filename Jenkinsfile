pipeline {
    agent { label 'Jenkins-Agent' }
	
	environment {
			APP_NAME = "nodehello"
            RELEASE = "1.0.0"
            DOCKER_USER = "traipatk"
            DOCKER_PASS = 'xxxx'
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
	
    stages{
		
		/*
        stage("Checkout from SCM"){
                steps {
                    git branch: 'main', credentialsId: 'github', url: 'https://github.com/traipat9k/node-hello-world.git'
                }
        }
		*/
		
        stage("Build Image"){
            steps {
				sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }

       }
	   
        stage("Push Image"){
           steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
				sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
				sh 'docker push ${IMAGE_NAME}:${IMAGE_TAG}'
				}
            }
       }
	   
	    stage("Trivy Scan") {
           steps {
               script {
	            sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image ${IMAGE_NAME}:${IMAGE_TAG} --no-progress --scanners vuln  --exit-code 0  --format table')
               }
           }
       }
	   
	    stage ('Cleanup Artifacts') {
           steps {
               script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    ;sh "docker rmi ${IMAGE_NAME}:latest"
               }
          }
       }
    }
}