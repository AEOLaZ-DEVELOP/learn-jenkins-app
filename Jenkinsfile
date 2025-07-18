pipeline {
                    agent any    
                    environment {                                                               
                        NETLIFY_SITE_ID = 'f0a528e5-4c88-46d0-b60b-f999e41f4092'                
                        NETLIFY_AUTH_TOKEN = credentials('netlify-token')  
                        REACT_APP_VERSION  = "1.0.$BUILD_ID"                
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
                                    echo "💥 stage build"
                                    ls -la
                                    node --version
                                    npm --version
                                    npm ci
                                    npm run build
                                    ls -la
                                '''
                            }
                        }
                        stage('Tests') {
                            parallel {
                                stage('Unit tests') {
                                    agent {
                                        docker {
                                            image 'node:18-alpine'
                                            reuseNode true
                                        }
                                    }
                                    steps {
                                        sh '''
                                            echo "💥 stage unit tests"
                                            #test -f build/index.html
                                            npm test
                                        '''
                                    }
                                    post {
                                        always {
                                            junit 'jest-results/junit.xml'
                                        }
                                    }
                                }
                                stage('E2E') {
                                    agent {
                                        docker {
                                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                                            reuseNode true
                                        }
                                    }

                                    steps {
                                        sh '''
                                            echo "💥 stage E2E"
                                            npm install serve
                                            node_modules/.bin/serve -s build &
                                            sleep 10
                                            npx playwright test  --reporter=html
                                        '''
                                    }
                                    post {
                                        always {
                                            publishHTML([
                                                allowMissing: false, 
                                                alwaysLinkToLastBuild: false, 
                                                keepAll: false, 
                                                reportDir: 'playwright-report', 
                                                reportFiles: 'index.html', 
                                                reportName: 'Playwright Local', 
                                                reportTitles: '', 
                                                useWrapperFileDirectly: true])
                                        }
                                    }
                                }
                            }
                        }
                        stage('deploy staging') {                     
                            agent {
                                docker {
                                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'                                      
                                    reuseNode true           
                                }
                            }
                            environment {
                                CI_ENVIRONMENT_URL = ' '                                                                    
                            }
                            steps {
                                sh '''
                                    echo "💥 stage deploy staging"
                                    npm install netlify-cli@17.17.0 node-jq
                                    node_modules/.bin/netlify --version
                                    echo "Deploying to staging. Site ID: $NETLIFY_SITE_ID"
                                    node_modules/.bin/netlify status
                                    node_modules/.bin/netlify deploy --dir=build --json > deploy-output.json
                                    export CI_ENVIRONMENT_URL=$(node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json)     
                                    npx playwright test  --reporter=html
                                '''
                                }
                            post {
                                always {
                                    publishHTML([
                                        allowMissing: false, 
                                        alwaysLinkToLastBuild: false, 
                                        icon: '', 
                                        keepAll: false, 
                                        reportDir: 'playwright-report', 
                                        reportFiles: 'index.html', 
                                        reportName: 'Staging E2E', 
                                        reportTitles: '', 
                                        useWrapperFileDirectly: true
                                    ])
                                }
                            }
                        }
                         stage('Deploy prod') {                     
                            agent {
                                docker {
                                     image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                                     reuseNode true  
                                }
                            }
                            environment {
                                    CI_ENVIRONMENT_URL = '  '
                            }
                            steps {
                                sh '''
                                    echo "💥 stage deploy prod"
                                    node --version
                                    npm install netlify-cli@17.17.0
                                    node_modules/.bin/netlify --version
                                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                                    export CI_ENVIRONMENT_URL=$(node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json)     
                                    node_modules/.bin/netlify deploy --dir=build --prod
                                    npx playwright test  --reporter=html                     
                                '''
                            }
                            post {
                                always {
                                    publishHTML([
                                        allowMissing: false, 
                                        alwaysLinkToLastBuild: false, 
                                        icon: '', 
                                        keepAll: false, 
                                        reportDir: 'playwright-report', 
                                        reportFiles: 'index.html', 
                                        reportName: 'Prod E2E', 
                                        reportTitles: '', 
                                        useWrapperFileDirectly: true
                                    ])
                                }
                            }
                        }
                    }    
                } 