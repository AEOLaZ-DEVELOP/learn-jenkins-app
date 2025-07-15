pipeline {
    agent none

    environment {
        NETLIFY_SITE_ID = 'f0a528e5-4c88-46d0-b60b-f999e41f4092'
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
                                    cat /etc/hostname
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
                    args '-v $WORKSPACE:/workspace -w /workspace'
                    reuseNode true
                }
            }
            steps {
                unstash 'build-artifact'
                sh '''
                     echo "---deploy stage---"
                    npm install netlify-cli node-jq
                    node_modules/.bin/netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status

                    echo "🔥 Checking build folder..."
                    ls -la build || echo "❌ build folder not found!"

                    echo "🚀 Starting deploy..."
                    node_modules/.bin/netlify deploy --dir=build --json --debug
                '''
            }
        }

    }
}
