pipeline {
    agent {
      label 'ecsEc2Agent'
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

    stage('Starting Container') {
      steps{
        script {
          
          // # Create a directory to store the container image artifacts
sh "mkdir kaniko"
sh "cd kaniko"

// # Create the Container Image Dockerfle
sh "cat << EOF > Dockerfile"
sh "FROM gcr.io/kaniko-project/executor:latest"
sh "COPY ./config.json /kaniko/.docker/config.json"
sh "EOF"

// # Create the Kaniko Config File for Registry Credentials
sh "cat << EOF > config.json"
sh "{ "credsStore": "ecr-login" }"
sh "EOF"
        }
      }
    }
  
    // // Building Docker images
    // stage('Building image') {
    //   steps{
    //     script {
    //       sh "docker build -t ${IMAGE_REPO_NAME}:${IMAGE_TAG} ."
    //     }
    //   }
    // }
   
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