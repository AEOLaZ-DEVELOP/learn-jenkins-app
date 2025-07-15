pipeline {
    agent none

    environment {
        NETLIFY_SITE_ID = 'f0a528e5-4c88-4d60-b60b-f999e41f4092'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

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
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
                stash includes: 'build/**', name: 'build-artifact'
            }
        }
stage('deploy staging') {
  agent {
    docker {
      image 'node:18-alpine'
      reuseNode true
    }
  }
  steps {
    sh '''
      # ติดตั้งเครื่องมือพื้นฐานให้ alpine
      apk add --no-cache bash curl git python3 make g++ libc6-compat

      npm install netlify-cli node-jq
      node_modules/.bin/netlify --version

      node_modules/.bin/netlify link --id=$NETLIFY_SITE_ID
      node_modules/.bin/netlify status
      node_modules/.bin/netlify deploy --dir=build --prod --json --debug
    '''
  }
}
    }
}
