pipeline {
    agent any
    
    tools {
        jdk 'JDK21'
        maven 'M3'
    }
    environment{
        // 환경변수 지정
        DOCKER_IMAGE_NAME = "spring-petclinic"

        // Credentials
        DOCKERHUB_CRED = credentials('dockerCredentials')
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
                sh '''
                docker build -t ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER} .
                docker tag ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER} ${DOCKERHUB_CRED_USR}/${DOCKER_IMAGE_NAME}:latest
                '''
            }
        }
        stage('Docker Hub Login') {
            steps {
                echo 'Docker Hub Login'
                sh 'echo ${DOCKERHUB_CRED_PSW} | docker login -u ${DOCKERHUB_CRED_USR} --password-stdin'
            }
        }
        stage('Docker Image Push') {
            steps {
                echo 'Docker Image Push'
                sh '''
                docker push ${DOCKERHUB_CRED_USR}/${DOCKER_IMAGE_NAME}:latest
                '''
            }
            post {
                always {
                    sh '''
                    docker rmi -f ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}
                    docker rmi -f ${DOCKERHUB_CRED_USR}/${DOCKER_IMAGE_NAME}:latest
                    '''
                }
            }
        }
        stage('Docker Container Run') {
            steps {
                echo 'Docker Container Run'
                sshPublisher(publishers: [sshPublisherDesc(configName: 'ubuntu',
                transfers: [sshTransfer(cleanRemote: false,
                excludes: '',
                execCommand: '''
                docker rm -f $(docker ps -aq)
                docker rmi -f $(docker images -q)
                docker run -itd -p 80:8080 --name spring-petclinic gouni4967/spring-petclinic:latest
                ''',
                execTimeout: 120000,
                flatten: false,
                makeEmptyDirs: false,
                noDefaultExcludes: false,
                patternSeparator: '[, ]+',
                remoteDirectory: '',
                remoteDirectorySDF: false,
                removePrefix: 'ubuntu',
                sourceFiles: '')],
                usePromotionTimestamp: false,
                useWorkspaceInPromotion: false,
                verbose: false)])
            }
        }
    }
}
