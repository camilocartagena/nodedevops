pipeline {
  agent any

  options {
    timeout(time: 2, unit: 'MINUTES')
  }

  environment {
    ARTIFACT_ID = "camilocartagena/nodedevops:${env.BUILD_NUMBER}"
  }

  stages {
    stage('sh how to') {
        steps {
            docker info
        }
    }
    stage('Build') {
      steps {
        script {
          dir("nodedevops") {
            dockerImage = docker.build "${env.ARTIFACT_ID}"
          }
        }
      }
    }
    stage('Run tests') {
      steps {
        sh "docker run ${dockerImage.id} npm test"
      }
    }
    stage('Publish') {
      when {
        branch 'master'
      }
      steps {
        script {
          docker.withRegistry("", "DockerHubCredentials") {
            dockerImage.push()
          }
        }
      }
    }
    stage('Schedule Staging Deployment') {
      when {
        branch 'master'
      }
      steps {
        build job: 'deploy-webapp-staging', parameters: [string(name: 'ARTIFACT_ID', value: "${env.ARTIFACT_ID}")], wait: false
      }
    }
  }
}

