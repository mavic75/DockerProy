def qa_server_ip="192.168.0.23";
def prod_server_ip="192.168.0.19";
pipeline {
    agent{label 'debian'} // debian node is QA environment
    stages {
        stage('Clone QA env') {
            steps {
                git 'https://github.com/mavic75/flask-vue-crud.git'
            }            
        }
        stage('Change frontend ip'){
            steps{
                dir('client'){
                    sh "cat src/components/Books.vue | grep 'http://'"
                    sh "sed -i 's/localhost/${qa_server_ip}/g' src/components/Books.vue"
                    sh "cat src/components/Books.vue | grep 'http://'"
                }
            }

        }
        stage('Deploy QA environment'){
            steps{
                git 'https://github.com/mavic75/flask-vue-crud.git'
                sh "docker compose down"
                sh "docker compose up -d"
                sh "docker compose ps"
            }
        }
        stage('Save docker images'){
            steps{
                sh "docker save backend:1.0 | gzip > backend1.0.tar.gz"
                sh "docker save frontend:1.0-prod | gzip > frontend1.0-prod.tar.gz"
                sh "ls -alth"
                stash includes: 'frontend1.0-prod.tar.gz', name: 'frontend_image-prod'
                stash includes: 'backend1.0.tar.gz', name: 'backend_image'
            }

        }
        stage ('Deploy PROD environment'){
            agent{label 'prod'}
            steps{
                git 'https://github.com/mavic75/flask-vue-crud.git'
                unstash 'backend_image'
                unstash 'frontend_image-prod'
                sh "docker compose -f docker-compose-prod.yml down"
                sh "echo 'Y' | docker image prune -a "
                sh "docker load -i frontend1.0-prod.tar.gz"
                sh "docker load -i backend1.0.tar.gz"
                sh "docker compose -f docker-compose-prod.yml up -d "
            }
        }
    }
}
