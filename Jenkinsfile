
pipeline{
    agent {
        docker {image 'maven:3.8.6-openjdk-11-slim'}     
    }
    tools{
        maven 'M2_HOME'
    }
    stages{
        stage("build & SonarQube analysis"){
            steps{
                withSonarQubeEnv('SonarServer'){
                    sh 'mvn sonar:sonar -Dsonar.projectKey=Thierry2023_geolocation2 -Dsonar.java.binaries=.'

                }
            }

        }
        
        stage('maven built'){
            steps{
                sh 'mvn clean'
                sh 'mvn install'
                sh 'mvn package'
            }

        }
          }
}
