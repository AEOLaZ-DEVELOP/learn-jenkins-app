pipeline {
    agent none

    environment {
        NETLIFY_SITE_ID = 'f0a528e5-4c88-46d0-b60b-f999e41f4092'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('deploy staging') {                     
            agent {
                 docker {
                     image 'mcr.microsoft.com/playwright:v1.39.0-jammy'   
                     reuseNode true           
                  }
            }
            steps {
               sh '''
                  ls -la
                  ls build
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
