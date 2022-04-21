pipeline {
    agent any
    environment {
        PROJECT_ID = 'kubernetes-project-340710'
        CLUSTER_NAME = 'cluster-2'
        LOCATION = 'asia-south1-a'
        CREDENTIALS_ID = 'jenkins-gke'
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
                    myapp = docker.build("omkarguj30/hello:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
		   withDockerRegistry([credentialsId: "gcr:GCR", url: "https://gcr.io"]) {
                      sh "docker push searce-playground-v1/rushabhapache:latest"
                    }
                    }
                }
            }
        }        
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
	}
     }
}
