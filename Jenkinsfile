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
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/rushabhmahale/jenkin-k8s.git']]])
            }
        }
        stage("Build image") {
            steps {
                script {
                      Img = docker.build(
                          "omkarguj30/hello:latest",
                          "-f Dockerfile ."
                          )
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    withDockerRegistry([credentialsId: "gcr:GCR", url: "https://gcr.io"]) {
                      sh "docker push omkarguj30/hello:latest"
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
