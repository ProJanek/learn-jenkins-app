pipeline {
    agent any
    
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
                    echo 'Building...'
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        stage('test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo 'Testing...'
                    test -f ./build/index.html
                    echo $?
                    npm test
                '''
            }
        }
    }
    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
