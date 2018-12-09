#!groovy
@Library('blockchyp-pipelines') _

pipeline {
  agent {
    docker {
      registryUrl 'https://105741232974.dkr.ecr.us-west-2.amazonaws.com'
      registryCredentialsId 'ecr:us-west-2:f4cba16d-79b0-4e7e-b771-921bf1ed54fa'
      image 'blockchyp/docs-build'
    }
  }

  stages {
    stage('Start') {
      steps {
        notifySlack 'Started'
      }
    }

    stage('Build Docs') {
      steps {
        sh 'cd docs && make html'
        sh "aws s3 sync --delete ${workspace}/docs/_build/html/ s3://blockchyp-prod-docs/"
      }
    }
  }

  post {
    always {
      notifySlack()
    }
  }
}
