pipeline {
    agent any
    
    tools {
        jdk 'jdk11'
        maven 'Maven 3.9.6'
    }
    
    environment {
        APP_NAME = 'MSA-Book' // 각 프로젝트마다 변경 필요
        GITHUB_REPO = 'https://github.com/pang2moa/MSA-Book.git' // 각 프로젝트마다 변경 필요
        BRANCH_NAME = 'master' // 또는 원하는 브랜치 이름
        SERVER_USER = 'appuser'
        SERVER_IP = '222.108.42.200'
        DEPLOY_PATH = '/opt/springapps/MSA-Book'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: "${BRANCH_NAME}", url: "${GITHUB_REPO}"
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    def jarFile = findFiles(glob: 'target/*.jar')[0]
                    
                    // 기존 프로세스 종료
                    sh "pkill -f ${APP_NAME} || true"
                    
                    // 새 JAR 파일 복사
                    sh "cp ${jarFile.path} ${DEPLOY_PATH}/${APP_NAME}.jar"
                    
                    // 새 프로세스 시작
                    sh "nohup java -jar ${DEPLOY_PATH}/${APP_NAME}.jar > ${DEPLOY_PATH}/${APP_NAME}.log 2>&1 &"
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}