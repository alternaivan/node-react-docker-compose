pipeline {
    agent { label 'm2c' }
    stages{
        stage('Cloning Develop branch') {
            steps {
                git branch: 'develop', credentialsId: 'github', url: 'git@github.com:alternaivan/node-react-docker-compose.git'
            }
        }
        stage('Stopping old and starting new containers') {
            steps {
                sh label: '', script: '/usr/local/bin/docker-compose -f docker-compose.prod.yml stop'
                sh label: '', script: '/usr/local/bin/docker-compose -f docker-compose.prod.yml up --no-start'
                sh label: '', script: '/usr/local/bin/docker-compose -f docker-compose.prod.yml start'
                //sh label: '', script: 'docker stop `docker ps -q`'
            }
        }
        stage('Testing Deployment') {
            steps {
                sh label: '', script: 'curl -s -o /dev/null -w "%{http_code}" http://marjanko2c.mylabserver.com:8080'
            }
            post('Restore working version from Master branch') {
                failure {
                    git credentialsId: 'github', url: 'git@github.com:alternaivan/node-react-docker-compose.git'
                    sh label: '', script: '/usr/local/bin/docker-compose -f docker-compose.prod.yml stop'
                    sh label: '', script: '/usr/local/bin/docker-compose -f docker-compose.prod.yml up --no-start'
                    sh label: '', script: '/usr/local/bin/docker-compose -f docker-compose.prod.yml start'
                    echo 'Test failed. Working version restored.'
                }
            }
        }
    }
}