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
                    # ลบ .netlify เดิมก่อน (กัน state เสีย)
                    rm -rf .netlify

                    # ทำการ link ใหม่แบบ CLI
                    node_modules/.bin/netlify link --id=$NETLIFY_SITE_ID || echo "⚠️ Link failed (อาจเคย link แล้ว)"

                    # ตรวจสอบ .netlify/state.json ว่ามีจริงไหม
                    cat .netlify/state.json || echo "⚠️ ยังไม่ได้ link จริง"
                    npm ci
                    npm install
                    npm run build
                    ls -la
                '''
                stash includes: 'build/**, .netlify/**', name: 'build-artifacts', allowEmpty: true
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
                    npm install netlify-cli
                    ls -la build
                    ls -la .netlify || echo "❌ .netlify not found"
                    node_modules/.bin/netlify status || echo "🔍 status failed"
                    node_modules/.bin/netlify deploy \
                        --dir=build \
                        --auth=$NETLIFY_AUTH_TOKEN \
                        --site=$NETLIFY_SITE_ID \
                        --json --debug
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
