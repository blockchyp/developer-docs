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
      sh 'cd docs && make html'
      s3Upload bucket: 'blockchyp-docs-prod', acl: 'Private', workingDir: "${workspace}/docs/_build/html", includePathPattern: "**/*", path: '/'
    }
  }

  post {
    always {
      notifySlack()
    }
  }
}
