                pipeline {
                    agent any    
                    environment {                                                               
                        NETLIFY_SITE_ID = 'f0a528e5-4c88-46d0-b60b-f999e41f4092'                
                        NETLIFY_AUTH_TOKEN = credentials('netlify-token')  
                        REACT_APP_VERSION  = "1.0.$BUILD_ID"  
                    }                                                                   
                    stages {
                        stage('aws') {
                            agent {
                                docker {
                                    image 'amazon/aws-cli'
                                    args "--entrypoint=''"                                              
                                }
                            }
                            steps {
                                withCredentials([
                                    usernamePassword(
                                        credentialsId: 'localstack-aws', 
                                        passwordVariable: 'AWS_SECRET_ACCESS_KEY',
                                        usernameVariable: 'AWS_ACCESS_KEY_ID'
                                    )
                                ]) {
                                    sh '''
                                        aws --endpoint-url=http://192.168.88.245:4566 \
                                           --region ap-southeast-1 \
                                            s3 rb s3://dev-artifact

                                        echo "🚀 upload artifact to bucket on localstack"
                                        echo "Hello S3!" > index.html
                                        aws --endpoint-url=http://192.168.88.245:4566 s3 cp index.html s3://dev-artifact/    
                                         
                                        
                                        echo "✅ list bucket localstack (--endpoint-url)"
                                        aws --endpoint-url=http://192.168.88.245:4566 s3 ls
                                    '''
                                }
                            }
                        }
                        // stage('build') {
                        //     agent {
                        //         docker {
                        //             image 'node:18-alpine'
                        //             reuseNode true
                        //         }
                        //     }
                        //     steps {
                        //         sh '''
                        //             echo "💥 stage build"
                        //             ls -la
                        //             node --version
                        //             npm --version
                        //             npm ci
                        //             npm run build
                        //             ls -la
                        //         '''
                        //     }
                        // }
                        // stage('test') {
                        //     parallel {
                        //         stage('unit tests') {
                        //             agent {
                        //                 docker {
                        //                     image 'node:18-alpine'
                        //                     reuseNode true
                        //                 }
                        //             }
                        //             steps {
                        //                 sh '''
                        //                     echo "💥 stage unit tests"
                        //                     #test -f build/index.html
                        //                     npm test
                        //                 '''
                        //             }
                        //             post {
                        //                 always {
                        //                     junit 'jest-results/junit.xml'
                        //                 }
                        //             }
                        //         }
                        //         stage('e2e') {
                        //             agent {
                        //                 docker {
                        //                     image 'my-playwright'
                        //                     reuseNode true
                        //                 }
                        //             }

                        //             steps {
                        //                 sh '''
                        //                     echo "💥 stage E2E"
                        //                     serve -s build &
                        //                     sleep 10
                        //                     npx playwright test  --reporter=html
                        //                 '''
                        //             }
                        //             post {
                        //                 always {
                        //                     publishHTML([
                        //                         allowMissing: false, 
                        //                         alwaysLinkToLastBuild: false, 
                        //                         keepAll: false, 
                        //                         reportDir: 'playwright-report', 
                        //                         reportFiles: 'index.html', 
                        //                         reportName: 'Playwright Local', 
                        //                         reportTitles: '', 
                        //                         useWrapperFileDirectly: true])
                        //                 }
                        //             }
                        //         }
                        //     }
                        // }
                        // stage('deploy staging') {                     
                        //     agent {
                        //         docker {
                        //             image 'my-playwright'                                           
                        //             reuseNode true           
                        //         }
                        //     }
                        //     environment {
                        //         CI_ENVIRONMENT_URL = ' '                                                                    
                        //     }
                        //     steps {
                        //         sh '''
                        //             echo "💥 stage deploy staging"
                        //             #npm install netlify-cli@17.17.0 node-jq                        
                        //             netlify --version                                               
                        //             netlify status                                                  
                        //             netlify deploy --dir=build --json > deploy-output.json
                        //             export CI_ENVIRONMENT_URL=$(jq -r '.deploy_url' deploy-output.json)   
                        //             npx playwright test  --reporter=html
                        //         '''
                        //         }
                        //     post {
                        //         always {
                        //             publishHTML([
                        //                 allowMissing: false, 
                        //                 alwaysLinkToLastBuild: false, 
                        //                 icon: '', 
                        //                 keepAll: false, 
                        //                 reportDir: 'playwright-report', 
                        //                 reportFiles: 'index.html', 
                        //                 reportName: 'Staging E2E', 
                        //                 reportTitles: '', 
                        //                 useWrapperFileDirectly: true
                        //             ])
                        //         }
                        //     }
                        // }
                        //  stage('Deploy prod') {                     
                        //     agent {
                        //         docker {
                        //              image 'my-playwright'                                          
                        //              reuseNode true  
                        //         }
                        //     }
                        //     environment {
                        //             CI_ENVIRONMENT_URL = '  '
                        //     }
                        //     steps {
                        //         sh '''
                        //             echo "💥 stage deploy prod"
                        //             netlify --version                                               
                        //             export CI_ENVIRONMENT_URL=$(jq -r '.deploy_url' deploy-output.json)    
                        //             netlify deploy --dir=build --prod                               
                        //             npx playwright test  --reporter=html                     
                        //         '''
                        //     }
                        //     post {
                        //         always {
                        //             publishHTML([
                        //                 allowMissing: false, 
                        //                 alwaysLinkToLastBuild: false, 
                        //                 icon: '', 
                        //                 keepAll: false, 
                        //                 reportDir: 'playwright-report', 
                        //                 reportFiles: 'index.html', 
                        //                 reportName: 'Prod E2E', 
                        //                 reportTitles: '', 
                        //                 useWrapperFileDirectly: true
                        //             ])
                        //         }
                        //     }
                        // }
                    }    
                }   