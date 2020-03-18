pipeline {
  agent any
  environment {
   DOCKER_IMAGE_NAME = "shaiksami179/train-schedule" 
  }
  stages {
    stage ('Build') {
      steps {
       echo "Hello World"
       sh './gradlew build --no-daemon'
       archiveArtifacts artifacts: 'dist/trainSchedule.zip'
      }
    }
    stage ('Docker Build Automation') {
      when {
       branch 'master' 
      }
      steps {
        script {
         app = docker.build(DOCKER_IMAGE_NAME)
          app.inside {
            sh 'echo Hello!,world'
          }
        }
      }
    }
    stage ('Push Docker Image to Registry') {
      when {
       branch 'master' 
      }
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
           app.push("${env.BUILD_NUMBER}")
           app.push('latest')
          }
        }
      }
    }
    stage ('Deploy to Production') {
      when {
       branch 'master' 
      }
      steps {
       input "Sure about Deploying to Production?"
       milestone(1)
        kubernetesDeploy (
         kubeconfigId: 'kubeconfig',
         configs: 'train-schedul-kube.yaml',
         enableConfigSubstitutions: true
        )
      }
    }
  }
}
