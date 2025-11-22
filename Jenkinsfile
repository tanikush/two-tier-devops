// Jenkinsfile (declarative)
pipeline {
  agent any
  
  environment {
    IMAGE_BACKEND = "tanishakushwah/two-tier-backend"
    IMAGE_FRONTEND = "tanishakushwah/two-tier-frontend"
    BUILD_NUMBER = "${env.BUILD_NUMBER}"
    GIT_COMMIT_SHORT = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
  }
  
  stages {
    stage('Checkout') {
      steps {
        checkout scm
        sh 'git clean -fdx'
      }
    }
    
    stage('Build Images') {
      parallel {
        stage('Build Backend') {
          steps {
            dir('backend') {
              sh "docker build -t ${IMAGE_BACKEND}:${BUILD_NUMBER} -t ${IMAGE_BACKEND}:latest ."
            }
          }
        }
        stage('Build Frontend') {
          steps {
            dir('frontend') {
              sh "docker build -t ${IMAGE_FRONTEND}:${BUILD_NUMBER} -t ${IMAGE_FRONTEND}:latest ."
            }
          }
        }
      }
    }
    
    stage('Test') {
      steps {
        script {
          // Test backend health
          sh "docker run --rm -d --name test-backend -p 3001:3000 ${IMAGE_BACKEND}:${BUILD_NUMBER}"
          sh "sleep 10"
          sh "curl -f http://localhost:3001/health || exit 1"
          sh "docker stop test-backend"
        }
      }
    }
    
    stage('Push Images') {
      when {
        anyOf {
          branch 'main'
          branch 'master'
        }
      }
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
          sh "docker push ${IMAGE_BACKEND}:${BUILD_NUMBER}"
          sh "docker push ${IMAGE_BACKEND}:latest"
          sh "docker push ${IMAGE_FRONTEND}:${BUILD_NUMBER}"
          sh "docker push ${IMAGE_FRONTEND}:latest"
        }
      }
    }
    
    stage('Deploy') {
      when {
        anyOf {
          branch 'main'
          branch 'master'
        }
      }
      steps {
        sh "docker-compose down || true"
        sh "docker-compose up -d --build"
        sh "sleep 30"
        sh "curl -f http://localhost:8080 || exit 1"
      }
    }
  }
  
  post {
    always {
      sh "docker system prune -f || true"
    }
    success {
      echo 'Pipeline succeeded!'
    }
    failure {
      echo 'Pipeline failed!'
    }
  }
}
