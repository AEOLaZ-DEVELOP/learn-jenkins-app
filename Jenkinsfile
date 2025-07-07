pipeline {
  agent any
  stages {
    stage('Build') {
      agent {
        docker {
          image 'node:18-alpine'
          reuseNode true
        }
      }
      steps {
        sh '''
          npm ci
          npm run build
        '''
      }
    }

    stage('Unit Test') {
      agent {
        docker {
          image 'node:18-alpine'
          reuseNode true
        }
      }
      steps {
        sh '''
          npm test
        '''
      }
    }

    stage('E2E') {
      agent {
        docker {
          image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
          reuseNode true
        }
      }
      steps {
        sh '''
          npm ci
          npx playwright install --with-deps
          npx serve -s build -l 5000 &
          sleep 5
          npx playwright test --reporter=junit
        '''
      }
    }
  }

  post {
    always {
      junit allowEmptyResults: true, testResults: 'test-results/junit.xml'
    }
  }
}
