// Function to validate that the message returned from SonarQube is ok
def qualityGateValidation(qg) {
  if (qg.status != 'OK') {
    // emailext body: "WARNING SANTI: Code coverage is lower than 80% in Pipeline ${BUILD_NUMBER}", subject: 'Error Sonar Scan,   Quality Gate', to: "${EMAIL_ADDRESS}"
    return true
  }
  // emailext body: "CONGRATS SANTI: Code coverage is higher than 80%  in Pipeline ${BUILD_NUMBER} - SUCCESS", subject: 'Info - Correct Pipeline', to: "${EMAIL_ADDRESS}"
  return false
}
pipeline {
  agent any

  tools {
      nodejs 'nodejs'
  }

  environment {
      // General Variables for Pipeline
      PROJECT_ROOT = 'app/'
      EMAIL_ADDRESS = 'san99tiagodevsecops@gmail.com'
      REGISTRY = 'bambino29/docker-node-demo'
      DOCKER_USER='bambino29'
      DOCKER_PASSWORD='Q1w2e3r4.'
  }

  stages {
      stage('Checkout') {
        steps {
        // Get Github repo using Github credentials (previously added to Jenkins credentials)
         checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/jquiroz29/demo']]]) 
        }
      }
      stage('Install dependencies') {
        steps {
          sh 'npm --version'
          sh "cd ${PROJECT_ROOT}; npm install"
        }
      }
      stage('Build docker-image') {
        steps {
          //sh "docker stop my-nodejs-app"
          //sh "docker rm my-nodejs-app"
          //sh "docker image rm  ${REGISTRY}:latest"
          sh "cd ./${PROJECT_ROOT};docker build -t ${REGISTRY}:${BUILD_NUMBER} . "
        }
      }
      stage('Deploy docker-image') {
        steps {
          // If the Dockerhub authentication stopped, do it again
          sh 'docker login -u ${DOCKER_USER} -p ${DOCKER_PASSWORD}'
          sh "docker run -p 3000:3000 -d --name my-nodejs-app ${REGISTRY}:latest"
          sh "docker start my-nodejs-app"
          sh "docker push ${REGISTRY}:latest"  
        }
      }
  }
}
