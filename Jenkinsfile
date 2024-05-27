pipeline {
    agent any

    environment {
        MAVEN_HOME = '/opt/apache-maven-3.9.6'
        PATH = "${MAVEN_HOME}/bin:${env.PATH}"
        JAVA_HOME = '/Users/gowe/Library/Java/JavaVirtualMachines/corretto-18.0.2/Contents/Home'
        DOCKER_REPO = 'gowthame/stash'
        DOCKERHUB_CREDENTIALS = credentials('DOCKERHUB')
        TIMESTAMP = sh(script: 'date "+%d-%m-%Y.%H.%M"', returnStdout: true).trim()
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/GowthamReddy-E/simple-java-maven-app.git', branch: 'main'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn -B  -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Build docker image') {
            steps {
                sh '/usr/local/bin/docker build -t ${DOCKER_REPO}:${BUILD_NUMBER}_${TIMESTAMP} .'
            }
        }
        stage('Login to Docker Hub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | /usr/local/bin/docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Push image') {
            steps {
                sh '/usr/local/bin/docker push ${DOCKER_REPO}:${BUILD_NUMBER}_${TIMESTAMP}'
            }
        }
        stage('Remove local image') {
            steps {
                sh '/usr/local/bin/docker rmi ${DOCKER_REPO}:${BUILD_NUMBER}_${TIMESTAMP}'
            }
        }
    }
}
