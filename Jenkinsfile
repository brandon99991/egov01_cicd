pipeline {
    agent any

    tools {
        //gradle 'gradle-7.6.1'
        maven 'Maven3.9.9'
        jdk 'JDK8'
    }

    environment {
        // 본인의 username으로 하실 분은 수정해주세요.
        //DOCKERHUB_USERNAME = 'brandon9999'
        GITHUB_URL = 'https://github.com/brandon99991/egov01_cicd.git'

        // 실습 넘버링
        CLASS_NUM = '2212'
    }
    
    stages {
        stage('소스파일 체크아웃') {
            steps {
                // 소스코드를 가져올 Github 주소
                git branch: 'main', url: 'https://github.com/brandon99991/egov01.git'
            }
        }

        stage('소스 빌드') {
            steps {
                // 755권한 필요 (윈도우에서 Git으로 소스 업로드시 권한은 644)
                //sh "chmod +x ./gradlew"
                //sh "gradle clean build"
                bat "mvn clean deploy"
            }
        }

        stage('릴리즈파일 체크아웃') {
            steps {
                checkout scmGit(branches: [[name: '*/main']],
                    extensions: [[$class: 'SparseCheckoutPaths',
                    sparseCheckoutPaths: [[path: "/${CLASS_NUM}"]]]],
					userRemoteConfigs: [[url: "${GITHUB_URL}"]])
            }
        }

    }
}