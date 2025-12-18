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
        // Source CheckOut
        stage('Source CheckOut') {
            steps {
                // 소스코드를 가져올 Github 주소
                git branch: 'main', url: 'https://github.com/brandon99991/egov01.git'
            }
        }

        // Source Build
        stage('Source Build') {
            steps {
                // 755권한 필요 (윈도우에서 Git으로 소스 업로드시 권한은 644)
                //sh "chmod +x ./gradlew"
                //sh "gradle clean build"
                bat "mvn clean deploy"
            }
        }

        // Release File CheckOut
        stage('Release File CheckOut') {
            steps {
                checkout scmGit(branches: [[name: '*/main']],
                    extensions: [[$class: 'SparseCheckoutPaths',
                    sparseCheckoutPaths: [[path: "/${CLASS_NUM}"]]]],
					userRemoteConfigs: [[url: "${GITHUB_URL}"]])
            }
        }

        // Deploy to Tomcat
        stage('Deploy to Tomcat') {
            steps {
                script {
                    sshPublisher(publishers: [
                        sshPublisherDesc(
                            configName: 'wsl-ssh',
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'target/*.war',
                                    remoteDirectory: 'webapps',
                                    flatten: true,
                                    execCommand: '''
                                        /bin/bash -lc '
                                            export JAVA_HOME="/home/user01/jdk/jdk1.8.0_77"
                                            export PATH="$JAVA_HOME/bin:$PATH"
                                            "/home/user01/tomcat.home/apache-tomcat-9.0.65/bin/shutdown.sh" || true
                                            sleep 5
                                            "/home/user01/tomcat.home/apache-tomcat-9.0.65/bin/startup.sh"
                                        '
                                    ''',
                                    execTimeout: 30000
                                )
                            ],
                            verbose: true
                        )
                    ])
                }
            }
        }

        // Restart to Tomcat
        stage('Restart to Tomcat') {
            agent { label 'wsl-jenkins' }
            options { skipDefaultCheckout(true) }
            steps {
                script {
                    sh "JENKINS_NODE_COOKIE=dontKillMe && sh /home/user01/tomcat.home/apache-tomcat-9.0.65/bin/shutdown.sh"
                    sh "sleep 3"
                    sh "JENKINS_NODE_COOKIE=dontKillMe && nohup sh /home/user01/tomcat.home/apache-tomcat-9.0.65/bin/startup.sh &"
                    sh "sleep 3"
                }
            }
        }




    }
}