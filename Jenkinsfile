pipeline {
    agent none
    tools{
        jdk 'myjava'
        maven 'mymaven'
    }
     environment{
        IMAGE_NAME='ajaynani117/demo:$BUILD_NUMBER'
        DEV_SERVER_IP='ec2-user@52.66.240.173'
        APP_NAME='java-mvn-app'
    }
    stages {
        stage('COMPILE') {
            agent any
            tools{
        jdk 'myjava'
        maven 'mymaven'
    }
            steps {
                script{
                    echo "COMPILING THE CODE"
                    git 'https://github.com/Ajaynani117/game-of-life.git'
         
                    tool name: 'mymaven', type: 'maven'
                    
                    sh 'mvn compile'
                }
                          }
            }
        stage('UNITTEST'){
            agent any
            steps {
                script{
                    echo "RUNNING THE UNIT TEST CASES"
                    sh 'mvn test'
                }
             
            }
            post{
                always{
                    junit 'target/surefire-reports/*.xml'
                }
            }
            }
        stage('PACKAGE+BUILD DOCKER IMAGE ON BUILD SERVER'){
            agent any
           steps{
            script{
            sshagent(['DEV_SERVER_KEY']) {
        withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                     echo "PACKAGING THE CODE"
                     sh "scp -o StrictHostKeyChecking=no server-script.sh ${DEV_SERVER_IP}:/home/ec2-user"
                     sh "ssh -o StrictHostKeyChecking=no ${DEV_SERVER_IP} 'bash ~/server-script.sh'"
                     sh "ssh ${DEV_SERVER_IP} sudo docker build -t  ${IMAGE_NAME} /home/ec2-user/addressbook"
                    sh "ssh ${DEV_SERVER_IP} sudo docker login -u $USERNAME -p $PASSWORD"
                        sh "ssh ${DEV_SERVER_IP} sudo docker push ${IMAGE_NAME}"
                        }
                        }
                    }
                }
            }

