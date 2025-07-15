pipeline {
    agent none

    environment {
        NETLIFY_SITE_ID     = 'f0a528e5-4c88-4d60-b60b-f999e41f4092'
        NETLIFY_AUTH_TOKEN  = credentials('netlify-token')  // อย่าลืม add ที่ Jenkins > Credentials
    }

    stages {
        stage('Build & Deploy') {
    agent {
        docker {
            image 'node:18-alpine'
            reuseNode true
        }
    }
    steps {
        sh '''
            echo "🚧 Building..."
            npm ci
            npm run build

            echo "📦 Installing netlify-cli"
            npm install netlify-cli

            echo "🔐 Setting token and linking"
            export NETLIFY_AUTH_TOKEN=$NETLIFY_AUTH_TOKEN
            node_modules/.bin/netlify link --id=$NETLIFY_SITE_ID

            echo "🚀 Deploying"
            node_modules/.bin/netlify deploy --dir=build --prod --auth=$NETLIFY_AUTH_TOKEN --site=$NETLIFY_SITE_ID
        '''
    }
}
    }
}
