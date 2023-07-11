pipeline{
    agent any
    tools{
        maven 'M2_HOME'
    }
    environment {
    registry = '232936573202.dkr.ecr.us-east-1.amazonaws.com/jenkins'
    registryCredential = 'aws_ecr_id'
    dockerimage = ''
    }
    stages{
        stage("build & SonarQube analysis"){
            agent {
                docker {image 'maven:3.8.6-openjdk-11-slim'}     
    }
            steps{
                withSonarQubeEnv('SonarServer'){
                    sh 'mvn sonar:sonar -Dsonar.projectKey=Thierry2023_geolocation -Dsonar.java.binaries=.'

                }
            }

        }
        
        stage('maven built'){
            steps{
                sh 'mvn clean'
                sh 'mvn install -DskipTests'
                sh 'mvn package -DskipTests'
            }

        }
        stage('Build Image') {
            steps {
                script{
                  def mavenPom = readMavenPom file: 'pom.xml'
                  POM_VERSION = "${mavenPom.version}"
                  echo "${POM_VERSION}"
                  dockerImage = docker.build registry + ":${POM_VERSION}"
                } 
            }
        }
        stage('push image') {
            steps{
                script{ 
                    docker.withRegistry("https://"+registry,"ecr:us-east-1:"+registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        } 
          }
}
