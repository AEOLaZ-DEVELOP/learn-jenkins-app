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
                    echo "🔧 Building..."
                    rm -rf .netlify
                    npm install netlify-cli
                    node_modules/.bin/netlify link --id=$NETLIFY_SITE_ID --debug
                    npm ci
                    npm run build
                    ls -la
                '''
                 stash includes: 'build/**', name: 'build-artifacts', allowEmpty: true
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
                    echo "🚀 Deploying to Netlify..."
                    ls -la .netlify || echo "⚠️ ยังไม่มีไฟล์ลิงก์"
                    node_modules/.bin/netlify deploy --dir=build \
                    --auth=$NETLIFY_AUTH_TOKEN \
                    --site=$NETLIFY_SITE_ID \
                    --json
                '''
                // script {
                //     env.staging_url = sh (
                //     script: "node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json",  
                //     returnStdout: true
                //     ).trim()
                // }
            }
        }
    }
}
