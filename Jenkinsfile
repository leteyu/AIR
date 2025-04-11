pipeline {
    agent any

    stages {
       
        stage('制品'){
         steps {

                 // some block
                 sh 'ls -al'
                 sh 'tar -zcvf openai.tar.gz *'
                 archiveArtifacts artifacts: 'openai.tar.gz',
                                                allowEmptyArchive: true,
                                                fingerprint: true,
                                                onlyIfSuccessful: true
                 sh 'ls -al'
         }
        }

        stage('部署'){
             steps {
                sh 'ls -al'
                writeFile file: 'Dockerfile',
                        text: '''FROM nginx
ADD openai.tar.gz /usr/share/nginx/html/
RUN rm -f /etc/nginx/conf.d/default.conf

RUN mv /usr/share/nginx/html/nginx.conf /etc/nginx/conf.d/default.conf
'''
                sh 'cat Dockerfile'
                sh 'docker build -f Dockerfile -t openai-app:latest .'
                sh 'docker rm -f openai-app'
                //推送到私有库中
                sh 'docker run -d -p 32506:80 --name openai-app openai-app:latest'
             }
        }
    }
}
