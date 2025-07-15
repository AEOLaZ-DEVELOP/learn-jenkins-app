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
                args "-v $WORKSPACE:/workspace -w /workspace"
                reuseNode true
                }
            }
            steps {
                unstash 'build-artifact'
                sh '''
                echo "🌍 Current workspace: $PWD"
echo "🔐 NETLIFY_SITE_ID=$NETLIFY_SITE_ID"
echo "🔐 NETLIFY_AUTH_TOKEN=${NETLIFY_AUTH_TOKEN:0:6}********"
echo "--- 📦 Checking files ---"
ls -la
ls -la build || echo "❌ build folder missing"
cat build/index.html || echo "⚠️ index.html not found"

echo "🛠 Installing netlify-cli + node-jq"
npm install netlify-cli node-jq

echo "🔗 netlify version check"
node_modules/.bin/netlify --version

echo "🔗 Linking project to Netlify..."
node_modules/.bin/netlify link --id=$NETLIFY_SITE_ID || echo "❌ link failed"

echo "📦 Checking Netlify status..."
node_modules/.bin/netlify status || echo "❌ status failed"

echo "🚀 Deploying..."
node_modules/.bin/netlify deploy --dir=build --prod --json --debug || echo "❌ deploy failed"
                '''
            }
        }
    }
}
