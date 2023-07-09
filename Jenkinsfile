pipeline{
    agent any
    tools{
        maven 'M2_HOME'
    }
    stages{
        stage('maven built'){
            steps{
                sh 'mvn clean install package'
            }

        }
        stage("build & SonarQube analysis"){
            agent any
            steps{
                withSonarQubeEnv('sonar'){
                    sh 'mvn sonar:sonar'
                }
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
                      nexusUrl: '192.168.55.10:8081',
                       nexusVersion: 'nexus3',
                        protocol: 'http',
                         repository: 'biom',
                          version: "${mavenPom.version}"
                }
            }
        }  
          }
}
