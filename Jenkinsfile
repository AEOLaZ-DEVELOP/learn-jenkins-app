pipeline {
    agent none

    environment {
        NETLIFY_SITE_ID     = 'f0a528e5-4c88-4d60-b60b-f999e41f4092'
        NETLIFY_AUTH_TOKEN  = credentials('netlify-token')  // อย่าลืม add ที่ Jenkins > Credentials
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
                    echo "🧱 Building..."
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
                stash includes: 'build/**', name: 'build-artifact'
            }
        }

        stage('Deploy to Netlify') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                unstash 'build-artifact'
                sh '''
                    echo "📦 Deploy stage started..."

                    # ตรวจสอบ working directory
                    echo "📁 Current workspace: $PWD"
                    ls -la build || echo "❌ Build folder not found!"

                    # ติดตั้ง CLI
                    echo "🛠 Installing netlify-cli + node-jq"
                    npm install netlify-cli node-jq

                    # Export token ให้ CLI อ่านได้
                    export NETLIFY_AUTH_TOKEN=$NETLIFY_AUTH_TOKEN
                    echo "TOKEN=$NETLIFY_AUTH_TOKEN"    

                    echo "🔗 Linking project"
                    node_modules/.bin/netlify link --id=$NETLIFY_SITE_ID || echo "❌ Link failed"

                    echo "🚀 Deploying..."
                    node_modules/.bin/netlify deploy \
                      --dir=build \
                      --prod \
                      --json \
                      --debug \
                      --auth=$NETLIFY_AUTH_TOKEN \
                      --site=$NETLIFY_SITE_ID || echo "❌ Deploy failed"
                '''
            }
        }
    }
}
