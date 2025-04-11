pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'openai-app'
        DOCKER_PORT = '32506'
    }

    stages {
        // 新增：从指定的GitHub仓库检出代码
        stage('Checkout') {
            steps {
                git url: 'https://github.com/leteyu/AIR.git', 
                    branch: 'main'  // 默认分支（根据实际情况修改为master或其他分支）
            }
        }

        stage('制品') {
            steps {
                sh 'ls -al'
                // 明确指定要打包的目录（例如 "dist" 或项目根目录）
                sh 'tar -zcvf openai.tar.gz .'  // 打包当前目录（排除不需要的文件如.git）
                archiveArtifacts artifacts: 'openai.tar.gz', 
                                allowEmptyArchive: true,
                                fingerprint: true,
                                onlyIfSuccessful: true
                sh 'ls -al'
            }
        }

        stage('部署') {
            steps {
                sh 'ls -al'
                // 将 Dockerfile 作为代码存入仓库，避免硬编码
                writeFile file: 'Dockerfile',
                          text: """FROM nginx
ADD openai.tar.gz /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/conf.d/default.conf
"""

                sh 'cat Dockerfile'
                sh 'docker build -f Dockerfile -t ${DOCKER_IMAGE}:latest .'
                
                // 清理旧容器
                sh 'docker rm -f ${DOCKER_IMAGE} || true'
                
                // 运行新容器
                sh "docker run -d -p ${DOCKER_PORT}:80 --name ${DOCKER_IMAGE} ${DOCKER_IMAGE}:latest"
                sh "docker run -d -p 8000:8000 -p 8787:8787 --restart=always -e HOST_IP=192.168.10.20  --name myrpg  rpggame
"
                
                // 推送到私有仓库的示例（如需）
                // sh 'docker tag ${DOCKER_IMAGE}:latest your-registry.com/${DOCKER_IMAGE}:latest'
                // sh 'docker push your-registry.com/${DOCKER_IMAGE}:latest'
            }
        }
    }
}
