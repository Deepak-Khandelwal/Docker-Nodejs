pipeline {
    agent { label 'kube_node' }
    environment {
        PROJECT_ID = 'PROJECT-ID'
        CLUSTER_NAME = 'CLUSTER-NAME'
        LOCATION = 'CLUSTER-LOCATION'
        CREDENTIALS_ID = 'gke'
    }
    stages {
        stage("Checkout code") {
            steps {
                git branch: 'master', url: 'https://github.com/Deepak-Khandelwal/Docker-Nodejs.git'
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("deepakkhandelwal/node-web-app:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }        
        stage('Kubernetes deployment') {
            steps{
                sh "sed -i 's/node-web-app:latest/node-web-app:${env.BUILD_ID}/g' deployment.yaml"
                script{
	            try{
                        sh "sudo kubectl apply -f deployment.yaml"
                   }catch(error){
			sh "sudo kubectl create -f deployment.yaml "
		   }
            }
        }
    }    
}
