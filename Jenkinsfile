pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: '153.73.144.245:8080', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '153.73.144.245:9090', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
     }

stages{
        stage('Build'){
 
        git url: 'https://github.com/cyrille-leclerc/multi-module-maven-project'

        withMaven(
            // Maven installation declared in the Jenkins "Global Tool Configuration"
            maven: 'maven53',
            // Maven settings.xml file defined with the Jenkins Config File Provider Plugin
            // Maven settings and global settings can also be defined in Jenkins Global Tools Configuration
            mavenSettingsConfig: 'maven53',
            mavenLocalRepo: '.repository') {

          // Run the maven build
          sh "mvn clean install"

        } // withMaven will discover the generated Maven artifacts, JUnit Surefire & FailSafe & FindBugs reports...
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
