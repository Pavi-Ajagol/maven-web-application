node{
	def mavenhome = tool name:"Maven 3.8.6"
	properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), 
	[$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
	echo "jenkins url is:${env. JENKINS_URL}"
	echo "node name is:${env. NODE_NAME}"
	echo "job name is:${env. JOB_NAME}"
	try{
	slacknotification('STARTED')
	stage ('checkoutCode'){
	git credentialsId: '72038918-1a19-46ac-a0a0-28af1157511a', url: 'https://github.com/Pavi-Ajagol/maven-web-application.git'
	}
	stage ('Build'){
	sh "${mavenhome}/bin/mvn clean package"
	}
	stage('ExecuteSonarQubeReport'){
	sh "${mavenhome}/bin/mvn clean sonar:sonar"
	}
	}//try block closing
	catch (e){
	slacknotification(currentBuild.result)
		throw e
	}
	finally{
		slacknotification(currentBuild.result)
	}
}//node closing
	
def slacknotification(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}
