pipeline {
    agent any
    tools {
        maven 'maven3'
        jdk 'jdk17'
    }
    environment{
        SCANNER= tool 'sonar-scanner'
    }
    stages {
        stage('Git Code Check') {
            steps {
                git branch: 'main', url: 'https://github.com/subho153/Valley-eCommerce-prototype.git'
            }
        }
        stage('Maven Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Maven Tests') {
            steps {
                sh 'mvn test -DskipTests=true'
            }
        }
        stage('Code Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh ''' $SCANNER/bin/sonar-scanner -Dsonar.projectName=Ecommerceprototype \
                    -Dsonar.projectKey=Ecommerceprototype \
                   -Dsonar.java.binaries=. '''
                }
            }
        }
        stage('Maven Build') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }
         stage('Docker Build & Push ') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker build -t image0 .'
                        sh 'docker tag image0 dockerhubdemosubha1234/ecommerce-prototype-cicd'
                        sh 'docker push dockerhubdemosubha1234/ecommerce-prototype-cicd'
                    }
                }
            }
        }
        stage('Docker Deploy') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker run -d --name eCommerceweb -p 8099:8099 dockerhubdemosubha1234/ecommerce-prototype-cicd'
                    }
                }
            }
        }
        stage('Deploy Application in Apache Tomcat') {
            steps {
                    sh 'cp /var/lib/jenkins/workspace/eCommerce-prototypeCICD/target/valley-1.0.jar /opt/apache-tomcat-9.0.65/webapps'
                }
        }
    }
}
