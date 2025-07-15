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
                        stage('tests') {
                            parallel {
                                stage('unit test') {                     
                                    agent {
                                        docker {
                                            image 'node:18-alpine'   
                                            reuseNode true           
                                        }
                                    }
                                    steps {
                                        sh '''
                                            test -f build/index.html                    
                                            npm test
                                        '''
                                    }
                                    post {                                                                          
                                        always {
                                            junit 'jest-results/junit.xml'                                      
                                        }
                                    }
                                }
                                stage('e2e') {                     
                                    agent {
                                        docker {
                                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                                            reuseNode true  
                                        }
                                    }
                                    steps {
                                        sh '''
                                            npm install serve
                                            node_modules/.bin/serve -s build &                          
                                            sleep 10
                                            npx playwright test --reporter=html                        
                                        '''
                                    }
                                    post {
                                        always {
                                            publishHTML(                                                        
                                                [allowMissing: false, 
                                                alwaysLinkToLastBuild: false, 
                                                icon: '', 
                                                keepAll: false, 
                                                reportDir: 'playwright-report', 
                                                reportFiles: 'index.html', 
                                                reportName: 'Playwright HTML Report', 
                                                reportTitles: '', 
                                                useWrapperFileDirectly: true]
                                            )
                                        }
                                    }
                                }
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