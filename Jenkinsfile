pipeline {
  agent { label 'exp-tools' }
  stages {
    stage('Tools') {
      steps {
        sh 'hostname'
        sh 'java -version'
        sh 'python --version'
        sh 'node --version'
        sh 'ansible --version | head -1'
        sh 'terraform version | head -1'
        sh 'aws --version'
      }
    }
  }
}