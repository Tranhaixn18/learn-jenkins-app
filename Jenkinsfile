pipeline {
    agent any

    environment {
        DOCKER_HOST = 'tcp://docker:2376'  // Kết nối tới jenkins-docker trong cùng network
        DOCKER_TLS_VERIFY = '1'           // Bật TLS
        DOCKER_CERT_PATH = '/certs/client'  // Đường dẫn chứng chỉ trong container
    }
    stages {
        // stage('build') {
        //     agent{
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //     }
        //     steps{
        //         sh '''
        //         ls -la
        //         node --version
        //         npm --version
        //         npm ci
        //         npm run build
        //         ls -la
        //         '''
        //     }
        // }
        stage('Test'){
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh '''
                npm ci
                #test -f build/index.html
                npm test
                '''
            }
        }
        
        stage('E2E'){
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
                npx playwright test
                '''
            }
        }
    }

    post {
        always{
            junit 'text-results/junit.xml'
        }
    }
}
