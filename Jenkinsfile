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
                checkout scm
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
        stage('Deploy to AWS') {
            steps{
                sh "sed -i 's/node-web-app:latest/node-web-app:${env.BUILD_ID}/g' deployment.yaml"
                script{
	            try{
                        sh "sudo kubectl apply -f ."
                   }catch(error){
			sh "sudo kubectl create -f . "
		   }
            }
        }
    }    
}
