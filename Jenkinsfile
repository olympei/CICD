node{
  
  //Define all variables
  def appName = 'todobackend'
  def serviceName = "${appName}-backend" 
  def imageTag = "mirna/${appName}:${env.BUILD_NUMBER}"
  
  //Checkout Code from Git
  checkout scm
  
  //Stage 1 : Build the docker image.
  stage('Build image') {
      sh("docker build -f Dockerfile-todobackend -t mirna/todobackend:${env.BUILD_NUMBER} .")
  }
  
  //Stage 2 : Push the image to docker registry
  stage('Push image to registry') {
	  withCredentials([usernamePassword( credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {

		docker.withRegistry('', 'dockerhub') {
		sh "docker login -u ${USERNAME} -p ${PASSWORD}"
		sh("docker push mirna/todobackend:${env.BUILD_NUMBER}")
			    

		  }
		  
	  }
  }
	
	
 //Stage 3 : Deploy Application
  stage('Deploy Application') {
	           sh("sed -i.bak '${imageTag}#' ./todobackend.yaml")
	           sh("kubectl create configmap postgres-config --from-literal=postgres.db.name=mydb")
	   	   sh("kubectl create secret generic db-security --from-literal=db.user.name=matthias --from-literal=db.user.password=password")
                   sh("kubectl apply -f postgres.yaml")
                   sh("kubectl apply -f todobackend.yaml")
                   
	    }	

  
 
  
 
}
