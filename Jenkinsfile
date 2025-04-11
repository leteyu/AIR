pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'openai-app'
        DOCKER_PORT = '32506'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/leteyu/AIR.git',
                    branch: 'main',
                    credentialsId: 'your-github-credentials'  // 如果仓库是私有的
            }
        }

        stage('制品') {
            steps {
                sh 'ls -al'
                sh 'tar -zcvf openai.tar.gz --exclude=.git dist/'
                archiveArtifacts artifacts: 'openai.tar.gz',
                                allowEmptyArchive: false,
                                fingerprint: true,
                                onlyIfSuccessful: true
                sh 'ls -al'
            }
        }

        stage('部署') {
            steps {
                sh 'ls -al'

                // 确保 nginx.conf 存在
                script {
                    if (!fileExists('nginx.conf')) {
                        error 'nginx.conf 未找到，请确认文件存在于仓库中！'
                    }
                }

                writeFile file: 'Dockerfile',
                          text: """FROM nginx
COPY openai.tar.gz /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/conf.d/default.conf
"""

                sh 'docker build -f Dockerfile -t ${DOCKER_IMAGE}:latest .'

                // 清理旧容器
                sh 'docker rm -f ${DOCKER_IMAGE} || true'
                sh 'docker rm -f myrpg || true'

                // 运行主应用容器
                sh "docker run -d -p ${DOCKER_PORT}:80 --name ${DOCKER_IMAGE} ${DOCKER_IMAGE}:latest"

                // 运行 RPG 游戏容器（修复后的命令）
                sh '''docker run -d
                    -p 8000:8000
                    -p 8787:8787
                    --restart=always
                    -e HOST_IP=192.168.10.20
                    --name myrpg
                    rpggame'''
            }
        }
    }

    post {
        always {
            sh 'docker system prune -f'
        }
    }
}
