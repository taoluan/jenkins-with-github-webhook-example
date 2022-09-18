pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    HEROKU_API_KEY = credentials('heroku-api-key')
    IMAGE_NAME = 'luan/jenkins-example-laravel-demo'
    IMAGE_TAG = 'latest'
    APP_NAME = 'jenkins-example-laravel-demo'
  }
  stages {
    stage("Env Build Number"){
      steps{
          echo "The build number is ${env.HEROKU_API_KEY}"
          echo "You can also use \${IMAGE_TAG} -> ${IMAGE_NAME}"
      }
    }

    stage('Build') {
      steps {
        bat 'docker build --tag $IMAGE_NAME:$IMAGE_TAG .'
      }
    }
    stage('Login') {
      steps {
        bat 'echo $HEROKU_API_KEY | docker login --username=_ --password-stdin registry.heroku.com'
      }
    }
    stage('Push to Heroku registry') {
      steps {
        bat '''
          docker tag $IMAGE_NAME:$IMAGE_TAG registry.heroku.com/$APP_NAME/web
          docker push registry.heroku.com/$APP_NAME/web
        '''
      }
    }
    stage('Release the image') {
      steps {
        bat '''
          heroku container:release web --app=$APP_NAME
        '''
      }
    }
  }
  post {
    always {
      bat 'docker logout'
    }
  }
}
