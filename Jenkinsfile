pipeline {
    agent any
    tools {
        maven 'maven35'
        jdk 'java8'
    }
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: '153.73.144.245:8080', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '153.73.144.245:9090', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
     }

stages{
        stage('Build'){
 
            steps {
                ansiColor("xterm") {
                    sh "mvn -B deploy"
                }
            }
      }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -i /home/jenkins/tomcat-demo.pem **/target/*.war tomcan@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /home/jenkins/tomcat-demo.pem **/target/*.war tomcat@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}
