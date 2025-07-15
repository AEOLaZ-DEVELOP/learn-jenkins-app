                pipeline {
                    agent any    
                    environment {                                                               
                        NETLIFY_SITE_ID = '6e0e72ab-213d-41ee-8920-75ed9c86bf89'                
                        NETLIFY_AUTH_TOKEN = credentials('netlify-token')                       
                    }                            
                    stages {
                        stage('build') {                     
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
                                    npm run build            
                                    ls -la
                                '''
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
                                sh '''
                                    npm install netlify-cli node-jq
                                    node_modules/.bin/netlify --version 
                                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                                    node_modules/.bin/netlify status
                                    node_modules/.bin/netlify deploy --dir=build --json 
                                '''
                                // script {
                                //     env.staging_url = sh (
                                //         script: "node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json",        
                                //         returnStdout: true
                                //     ).trim()
                                // }
                            }
                        }
                    }    
                } 