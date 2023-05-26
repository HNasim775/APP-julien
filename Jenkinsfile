pipeline{
    agent any
    stages{
        stage('Tests'){
            steps{
                script{
                    docker.image('node').inside{
                        sh '''
                        echo 'Build...'
                        npm install
                        echo 'Test...'
                        npm test
                        '''
                    }
                }
            }
        }
        stage('Build and Push Docker Image'){
            steps{
                script{
                    def app = docker.build("nodejd:master", ".")
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        app.push('master')
                    }
                }
            }
        }
        stage('Deploy Image'){
            steps{
                script{
                    sh '''
                    docker stop nodejd || true
                    docker rm nodejd || true
                    docker rmi nodejd || true
                    docker tag nodejd:master nodejd:prod
                    docker run -d --name nodejd -p 3000:3000 nodejd:prod
                    '''
                }
            }
        }
    }
}
