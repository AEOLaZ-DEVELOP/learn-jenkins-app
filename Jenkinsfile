pipeline {
    agent any 
    environment {
        NETLIFY_SITE_ID = 'f0a528e5-4c88-46d0-b60b-f999e41f4092'
        NETLIFY_AUTH_TOKEN = credentials('netlify_token')
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
                    npm install netlify-cli node-jq                                                                                        
                    node_modules/.bin/netlify --version
                    echo "Deploying to staging. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status                           
                    node_modules/.bin/netlify deploy --dir=build --json > deploy-output.json        
                    node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json                   
                '''
            }
        }
    }    
} 