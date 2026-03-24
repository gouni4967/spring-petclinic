pipeline {
    agent any
    
    tools {
        jdk 'JDK21'
        maven 'M3'
    }
    stages {
        stage('Git Clone') {
            steps {
                git url:'https://github.com/gouni4967/spring-petclinic.git',
                branch: 'main'
            }
        }
        stage('Maven Build') {
            steps {
                sh 'mvn clean package -Dmaven.test.failure.ignore=true'
            }
        }
        stage('Docker Image Create') {
            steps {
                echo 'Docker Image Create'
            }
        }
        stage('Docker Hub Login') {
            steps {
                echo 'Docker Hub Login'
            }
        }
        stage('Docker Image Push') {
            steps {
                echo 'Docker Image Push'
            }
        }
        stage('Docker Container Run') {
            steps {
                echo 'Docker Container Run'
            }
        }
    }
}
