def tag, dockerUser, containerName, httpPort = ""
node(){
    stage('Prepare Environment'){
        echo 'Initialize Environment'
        tag="1.0"
	withCredentials([usernamePassword(credentialsId: 'dockerACRAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
		dockerUser="$dockerUser"
        }
	containerName="backendapp"
	httpPort="8989"
    }
    
    stage('Code Checkout'){
        try{
            checkout scm
        }
        catch(Exception e){
            echo 'Exception occured in Git Code Checkout Stage'
            currentBuild.result = "FAILURE"
        }
    }
	
    stage('Maven Build'){
        sh "mvn clean package"        
    }
	
    stage('Docker Image Build'){
        echo 'Creating Docker image'
        sh "docker build -t $dockerUser/$containerName:$tag --pull --no-cache ."
    }  

    stage('Publishing Image to ACR'){
        echo 'Pushing the docker image to ACR'
        withCredentials([usernamePassword(credentialsId: 'dockerACRAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
		sh "docker login -u $dockerUser -p $dockerPassword"
		sh "docker push $dockerUser/$containerName:$tag"
		echo "Image push complete"
        } 
    }
}
