pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
           steps{
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
        stage('Test'){
            agent{docker{
                image 'node:18-alpine'
                reuseNode true
                        }
                }
            steps{
                echo "Test Stage"
                sh '''
                echo "Test Stage2"
                test -d build
                npm --version
                ls-la
                npm test
                '''
            }
        }
    }
}
