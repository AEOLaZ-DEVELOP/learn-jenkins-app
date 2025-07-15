pipeline {
    agent none

    environment {
        NETLIFY_SITE_ID     = 'f0a528e5-4c88-4d60-b60b-f999e41f4092'
        NETLIFY_AUTH_TOKEN  = credentials('netlify-token')  
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
            npm install netlify-cli node-jq
            node_modules/.bin/netlify --version 
            echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
            node_modules/.bin/netlify status
            node_modules/.bin/netlify deploy --dir=build --json 
            '''
            }
        }
    }
}
