pipeline { 
    agent any
    parameters { 
string(name: 'GIT_URL', defaultValue: 'https://github.com/Bhavani4444/mavenproj.git', description: '') 
string(name: 'GIT_BRANCH', defaultValue: 'master', description: '')
string(name: 'MAVEN_GOAL', defaultValue: 'package', description: '')
}
     stages{
        stage("git init"){ 
          steps{ 
            checkout scmGit(branches: [[name: '*/${GIT_BRANCH}']], extensions: [], userRemoteConfigs: [[url: 
'${GIT_URL}']])     
          }
        }
        stage("maven"){ 
          steps{ 
            sh 'mvn ${MAVEN_GOAL}'
          }
        } 
        stage("sonarqube"){ 
          steps{ 
            sh '''mvn sonar:sonar \\
  -Dsonar.projectKey=scriptedpipeline \\
  -Dsonar.host.url=http://54.162.223.206:9000 \\
  -Dsonar.login=7a4b5b4ef50c00e817901c897659745d491de13f'''      
          }
        }
        stage("nexus"){ 
          steps{ 
            nexusArtifactUploader artifacts: [[artifactId: '$BUILD_TIMESTAMP', classifier: '', file: 'webapp/target/webapp.war', type: 'war']], credentialsId: 'NEXUS_CREDS', groupId: 'DEV', nexusUrl: '18.204.20.222:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'nexus-releases', version: '$BUILD_ID' 
          }
        }
        stage("tomcat"){ 
          steps{
            deploy adapters: [tomcat9(credentialsId: 'TOMCAT_CREDS', path: '', url: 'http://44.203.0.26:8080')], contextPath: 'devenvtest', war: '**/*.war' 
          }
        }
     }
}
