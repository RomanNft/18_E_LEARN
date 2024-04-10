#!groovy
// Groovy Jenkinsfile

properties([disableConcurrentBuilds()])

pipeline {
    agent {
        label ''
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
        timestamps()
    }

    stages {
        stage("Create docker image") {
            steps {
                echo 'Creating docker image ...'
                dir('.') {
                    sh "docker build --no-cache -t roman2447/website:1.1 ."
                }
            }
        }
        
        stage("docker login") {
            steps {
                echo " ============== docker login =================="
                withCredentials([usernamePassword(credentialsId: 'DockerHub-Credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                    docker login -u $USERNAME -p $PASSWORD
                    '''
                }
            }
        }

        stage("docker push") {
            steps {
                echo " ============== pushing image =================="
                sh '''
                docker push roman2447/website:1.1
                '''
            }
        }

        stage("docker stop and remove previous container") {
            steps {
                echo " ============== stopping and removing previous container =================="
                sh '''
                docker stop website
                docker rm website
                '''
            }
        }

        stage("docker run") {
            steps {
                echo " ============== start server =================="
                sh '''
                docker run -d --restart=always --name website -p 80:80 roman2447/website:1.1
                '''
            }
        }
    }
}
