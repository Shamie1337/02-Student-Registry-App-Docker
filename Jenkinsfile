pipeline {
  agent any
  options { timestamps() }

  stages {
    stage('Checkout'){ steps { checkout scm } }
    stage('Setup Node.js'){
      steps { nodejs(nodeJSInstallationName: 'Node 20'){ sh 'node -v && npm -v' } }
    }
    stage('Install Dependencies'){
      steps { nodejs(nodeJSInstallationName: 'Node 20'){ sh 'npm ci' } }
    }
    stage('Start Application'){
      steps { nodejs(nodeJSInstallationName: 'Node 20'){ sh '''
        npm run build || true
        nohup npm start > app.log 2>&1 &
        echo $! > app.pid
        sleep 3
      ''' } }
    }
    stage('Run Tests'){
      steps { nodejs(nodeJSInstallationName: 'Node 20'){ sh 'npm test' } }
    }
  }
  post {
    always {
      sh 'test -f app.pid && kill "$(cat app.pid)" || true'
      archiveArtifacts artifacts: 'app.log', allowEmptyArchive: true
    }
  }
}
