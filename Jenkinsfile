pipeline {
  agent any
  options { timestamps() }

  stages {
    stage('Checkout') { steps { checkout scm } }

    stage('Setup Node.js') {
      steps {
        sh '''
          export PATH="/opt/homebrew/opt/node@20/bin:/opt/homebrew/bin:$PATH"
          which node
          node -v && npm -v
        '''
      }
    }

    stage('Install Dependencies') { steps { sh 'npm ci' } }

    stage('Start Application') {
      steps {
        sh '''
          npm run build || true
          nohup npm start > app.log 2>&1 &
          echo $! > app.pid
          sleep 3
        '''
      }
    }

    stage('Run Tests') { steps { sh 'npm test' } }
  }

  post {
    always {
      sh 'test -f app.pid && kill "$(cat app.pid)" || true'
      archiveArtifacts artifacts: "app.log", allowEmptyArchive: true
    }
  }
}
