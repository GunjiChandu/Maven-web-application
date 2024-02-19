node {
  def mavenHome = tool name: "maven3.9.6"
  properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '4')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

  echo "Node name is:${env.NODE_NAME}"
  echo "Brach name is:${env.BRANCH_NAME}"
  echo "Job name is:${env.JOB_NAME}"
  echo "Build no Is:${env.BUILD_NUMBER}"
  try {
    stage('CheckoutCode') {
      git branch: 'development', credentialsId: '93637183-9c82-4527-a1a2-ce5ba6c121ea', url: 'https://github.com/GunjiChandu/maven-web-application.git'
    }
    stage('buildpackage') {
      sh "${mavenHome}/bin/mvn clean package"

    }
    stage('ExecutedSonarQube') {
      sh "${mavenHome}/bin/mvn clean sonar:sonar"
    }
    stage('uploadArtifactsNexus') {
      sh "${mavenHome}/bin/mvn deploy"
    }
    stage('deployToTomcatserver') {
      sshagent(['0bcdcdd6-103a-4866-95cb-3716b0b35142']) {
        sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war  ec2-user@172.31.91.116:/opt/tomcat9/webapps/"
      }
    }
    }catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
    throw e
  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
}
def notifyBuild(String buildStatus = 'STARTED') {
     // build status of null means successful
     buildStatus = buildStatus ?: 'SUCCESS'
     // Default values
     def colorName = 'RED'
     def colorCode = '#FF0000'
     def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
     def summary = "${subject} (${env.BUILD_URL})"
     // Override default values based on build status
     if (buildStatus == 'STARTED') {
       color = 'YELLOW'
       colorCode = '#FFFF00'
     } else if (buildStatus == 'SUCCESS') {
       color = 'GREEN'
       colorCode = '#00FF00'
     } else {
       color = 'RED'
       colorCode = '#FF0000'
     }
     // Send notifications
     slackSend (color: colorCode, message: summary)
    }
 
