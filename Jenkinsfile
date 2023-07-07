properties([pipelineTriggers([githubPush()])])
pipeline{
    agent {
        docker { image 'maven:3.8.6-eclipse-temurin-8-alpine' }
    }
<<<<<<< HEAD
   agent any
    tools {
  maven 'M2_HOME'
}
environment {
    registry = '076892551558.dkr.ecr.us-east-1.amazonaws.com/jenkins'
    registryCredential = 'jenkins-ecr'
    dockerimage = ''

     NEXUS_VERSION = "nexus3"
     NEXUS_PROTOCOL = "http"
     NEXUS_URL = "139.177.192.139:8081"
     NEXUS_REPOSITORY = "utrains-nexus-pipeline"
     NEXUS_CREDENTIAL_ID = "nexus-user-credentials"
     POM_VERSION = ''
}
    stages {

        stage("build & SonarQube analysis") {  
            steps {
                echo 'build & SonarQube analysis...'
               withSonarQubeEnv('SonarServer') {
                   sh 'mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=kserge2001_geolocation -X'
               }
            }
          }
        stage('Check Quality Gate') {
            steps {
                echo 'Checking quality gate...'
                 script {
                     timeout(time: 20, unit: 'MINUTES') {
                         def qg = waitForQualityGate()
                         if (qg.status != 'OK') {
                             error "Pipeline stopped because of quality gate status: ${qg.status}"
                         }
                     }
                 }
            }
        }
        
         
        stage('maven package') {
            steps {
                sh 'mvn clean'
                sh 'mvn package -DskipTests'
=======
    tools {
        maven 'maven3'
    }
    stages{
        stage('mavin build'){
            steps{
                sh 'mvn clean install package'
            }

        }
        stage('upload artifact'){
            steps{
                script{
                    def mavenPom = readMavenPom file: 'pom.xml'
                    nexusArtifactUploader artifacts: 
                    [[artifactId: "${mavenPom.artifactId}",
                    classifier: '',
                    file: "target/${mavenPom.artifactId}-${mavenPom.version}.${mavenPom.packaging}",
                    type: "${mavenPom.packaging}"]],
                    credentialsId: 'NexusID',
                    groupId: "${mavenPom.groupId}",
                     nexusUrl: '45.33.7.113:8081',
                      nexusVersion: 'nexus3',
                       protocol: 'http',
                        repository: 'biom',
                         version: "${mavenPom.version}" 
                }
            }

        }
        stage('list the dir'){
            steps{
                sh ' pwd'
>>>>>>> e0bf4ad4c98f7215082391afc63ffb90c4ad8b70
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

        // Project Helm Chart push as tgz file
        stage("pushing the Backend helm charts to nexus"){
            steps{
                script{
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:'nexus-pass', usernameVariable: 'jenkins-user', passwordVariable: 'docker_pass']]) {
                            def mavenPom = readMavenPom file: 'pom.xml'
                            POM_VERSION = "${mavenPom.version}"
                            sh "echo ${POM_VERSION}"
                            sh "tar -czvf  app-${POM_VERSION}.tgz app/"
                            sh "curl -u jenkins-user:$docker_pass http://139.177.192.139:8081/repository/geolocation/ --upload-file app-${POM_VERSION}.tgz -v"  
                    }
                } 
            }
        }     	    
    }
}
