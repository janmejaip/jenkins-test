pipeline {
    agent {
      label 'ecsAgent'
    }
    environment {
        AWS_ACCOUNT_ID="242865541181"
        AWS_DEFAULT_REGION="us-east-1" 
        IMAGE_REPO_NAME="jenkins-build-pipeline"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }
   
    stages {
        
         stage('Logging into AWS ECR') {
            steps {
                script {
                sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
                }
                 
            }
        }
        
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/janmejaip/jenkins-test.git']]])     
            }
        }

   // Building Docker images
    stage('Buildah Status') {
      steps{
        script {
          sh "buildah version"
        }
      }
    }
  
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          sh "mkdir build"
          sh "mkdir /var/lib/containers1"
          sh "podman run -v ./build:/build:z -v /var/lib/containers1:/var/lib/containers:Z quay.io/buildah/stable buildah  -t ${IMAGE_REPO_NAME}:${IMAGE_TAG} bud /build"
        }
      }
    }
   
    // // Uploading Docker images into AWS ECR
    // stage('Pushing to ECR') {
    //  steps{  
    //      script {
    //             sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
    //             sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
    //      }
    //     }
    //   }
    }
}