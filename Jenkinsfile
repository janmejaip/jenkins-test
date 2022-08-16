pipeline {
    agent {
      label 'ecsAgent'
    }
    environment {
        AWS_ACCOUNT_ID="583829956256"
        AWS_DEFAULT_REGION="us-east-1" 
        IMAGE_REPO_NAME="jenkins-build-pipeline"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }
   
    stages {
        
         stage('Logging into AWS ECR') {
            steps {
                script {

                sh "cat /root/.aws/credentials"

                sh "cat /kaniko/.docker/credentials"
                // sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION}"

                }
                 
            }
        }
        
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/janmejaip/jenkins-test.git']]])     
            }
        }

   
  
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          // sh "buildah  --storage-driver vfs --security-opt seccomp=unconfined --security-opt label:disabled bud --format docker --isolation=chroot -f Dockerfile -t ${IMAGE_REPO_NAME}:${IMAGE_TAG} ."
          // sh "docker run -it -e _BUILDAH_STARTED_IN_USERNS="" -e BUILDAH_ISOLATION=chroot --security-opt seccomp=unconfined --security-opt label:disabled quay.io/buildah/stable:latest /bin/sh"
          sh "export DOCKER_CONFIG=/kaniko/.docker"
          sh "/kaniko/executor --ignore-path=/root/.aws --context \$(pwd) --dockerfile \$(pwd)/Dockerfile --destination ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG} --destination ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:latest --force"
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