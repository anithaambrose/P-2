pipeline {
  agent any

  environment {
        IMAGE = 'anitodevops/trendstore'
        DOCKER_CRED_ID = 'dockerhub-creds'
  }

  triggers {
       githubPush ()
  }

  stages {

    stage('Build') {
      steps {
        script {
          sh "docker build -t ${IMAGE}:latest ." 
        }
      }
    }

    stage('Push') {
      steps {
        script {

          withCredentials([usernamePassword(credentialsId: "${DOCKER_CRED_ID}", usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]){
            sh """
              echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
              docker push ${IMAGE}:latest
            """
          }
        }
      }
    } 

    stage('Deploy') {
      steps {
        script {
				withAWS(credentials: 'aws-creds') {
	                sh """
	                  aws eks update-kubeconfig --name trendstore --region ap-south-1
					  kubectl apply -f deployment.yml 
					  kubectl apply -f service.yml 
	                """
				}
		}
	  }
	}
  }
}
