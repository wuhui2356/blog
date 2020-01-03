pipeline {
  agent any
  stages {
    stage('检出') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: env.GIT_BUILD_REF]], 
                                                                                                                                            userRemoteConfigs: [[url: env.GIT_REPO_URL, credentialsId: env.CREDENTIALS_ID]]])
      }
    }
    stage('构建') {
      steps {
        echo '构建中...'
        echo '$GIT_DEPLOY_KEY'
        sh 'docker version'
        sh 'node -v'
        sh 'npm install -g hexo-cli'
        sh 'npm install hexo --save'
        sh 'hexo clean'
        sh 'hexo generate'
        echo '构建完成.'
      }
    }
    stage('部署') {
      steps {
        echo '部署中...'
        sh 'ls -l'
        sh 'npm install hexo-deployer-git --save'
        sh 'hexo deploy'
        echo '部署完成'
      }
    }
  }
}