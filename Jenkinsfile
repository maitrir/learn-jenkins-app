pipeline {
    agent any
    environment{
        NETLIFY_SITE_ID = '4e6d3357-7e8a-46f9-9aa3-3e2dfbec0e07'
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
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        
        stage ('Test'){
            parallel{
                stage('Unit Test') {
                        agent {
                            docker {
                                image 'node:18-alpine'
                                reuseNode true
                            }
                        }

                        steps {
                            sh '''
                                #test -f build/index.html
                                npm test
                            '''
                        }
                        post {
                            always {
                                junit 'jest-results/junit.xml'
                            //  publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
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
                        steps{
                                sh '''
                                    npm install serve
                                    node_modules/.bin/serve -s build &
                                    sleep 10
                                    npx playwright test --reporter=html
                                '''
                             }
                        post {
                                    always {
                            // junit 'jest-results/junit.xml'
                                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                                }
                            }
                    }
    
                }

            }
   
    
    stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli 
                    node_modules/.bin/netlify --version
                    echo "Deploying to production.Site_ID : $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status 
                    node_modules/.bin/netlify deploy --dir=build --prod

                '''
            }
        }
    }  
}
