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
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm install
                    npm run build
                    ls -la
                '''
                stash includes: 'build/**', name: 'build-artifacts'
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
                unstash 'build-artifacts'
                sh '''
                    npm install netlify-cli node-jq
                    node_modules/.bin/netlify --version 
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --auth=$NETLIFY_AUTH_TOKEN --site=$NETLIFY_SITE_ID --json --debug
                '''
                script {
                    env.staging_url = sh (
                    script: "node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json",  
                    returnStdout: true
                    ).trim()
                }
            }
        }
    }
}
