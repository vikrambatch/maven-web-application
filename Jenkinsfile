node
{
    def mavenHome = tool name: "maven3.8.6"
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), pipelineTriggers([pollSCM('* * * * *')])])
    echo "The Job name is: ${env.JOB_NAME}"
    echo "The Node name is: ${env.NODE_NAME}"
    echo "The workspace path is: ${env.WORKSPACE}"
    echo "The node label is: ${env.NODE_LABELS}"
    echo "The build number is: ${env.BUILD_NUMBER}"

 try{
stage('CheckoutCode'){
        
    git branch: 'development', credentialsId: '5e38a476-8c9e-4bd3-8359-96460d3f73ce', url: 'https://github.com/vikrambatch/maven-web-application.git'
}
stage('Build'){
    sh "${mavenHome}/bin/mvn clean package"
}
  
  /*
stage('ExecuteSonarQubeReport'){
    sh "${mavenHome}/bin/mvn sonar:sonar"
}

stage('UploadArtifactsIntoNexus'){
    sh "${mavenHome}/bin/mvn deploy"
}
stage('DeployAppIntoTomcatServer'){
    sshagent(['10a5bf78-cbc8-4a46-bc9d-4cab9e3aee52']) {
    sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@3.6.91.111:/opt/apache-tomcat-9.0.65/webapps/"
}

}
*/
}
 catch (e) {
    currentBuild.result = "FAILURE"
    throw e
  }
    finally {
    slacknotifications(currentBuild.result)
    }
}
def slacknotifications(String buildStatus = 'STARTED') {
  buildStatus =  buildStatus ?: 'SUCCESS'

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
