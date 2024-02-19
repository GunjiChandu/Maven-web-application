node{
    def mavenHome = tool name: "maven3.9.6"
    
    echo "Node name is:${env.NODE_NAME}"
    echo "Brach name is:${env.BRANCH_NAME}"
    echo "Job name is:${env.JOB_NAME}"
    echo "Build no Is:${env.BUILD_NUMBER}"
    
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '3', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

   stage('CheckoutCode') {
       git branch: 'development', credentialsId: '93637183-9c82-4527-a1a2-ce5ba6c121ea', url: 'https://github.com/GunjiChandu/maven-web-application.git'
   }
   stage('buildpackage'){
       sh "${mavenHome}/bin/mvn clean package"
       
   }
   stage('ExecutedSonarQube'){
     sh"${mavenHome}/bin/mvn clean sonar:sonar"
   }
   stage('uploadArtifactsNexus'){
    sh "${mavenHome}/bin/mvn deploy"
   }
   stage('DeploytoTomcatserver'){
       sshagent(['0bcdcdd6-103a-4866-95cb-3716b0b35142']) {
          sh"scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.91.116:/opt/tomcat9/webapps/"
   }
   }
}
