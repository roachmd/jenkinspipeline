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
            withMaven(
                // Maven installation declared in the Jenkins "Global Tool Configuration"
                maven: 'M3',
                // Maven settings.xml file defined with the Jenkins Config File Provider Plugin
                // Maven settings and global settings can also be defined in Jenkins Global Tools Configuration
                mavenSettingsConfig: 'maven35',
                mavenLocalRepo: '.repository')
            steps {
                sh 'mvn clean package -Dmaven.repo.local=.m2'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
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
