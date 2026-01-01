pipeline {
    agent any
    
    stages {
        // This is a comment
        /*
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
        */
        stage('test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    # comment for shell
                    echo 'Testing...'
                    test -f ./build/index.html
                    echo $?
                    npm test
                '''
            }
        }
        stage('e2e test') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.57.0-noble'
                    reuseNode true
                    // args '-u root:root' -> running container as root is not a good idea (changes mage by root can be not available for standard user)
                }
            }
            steps {
                sh '''
                    # install serve as local depedency (not global with -g flag)
                    # npm install -g serve
                    # serve -s build
                    npm install serve
                    # & will start server in background
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npx playwright test
                '''
            }
        }
    }
    post {
        always {
            junit 'junit-results/junit.xml'
        }
    }
}
