node {
   echo "git branch name: ${env.JOB_NAME}"
   echo "build number is: ${env.BUILD_NUMBER}"
   echo "node name is: ${env.NODE_NAME}"
    
	def mavenhome=tool name : "maven"
	try{

	
	stage('git clone'){
	notifyBuild('STARTED')
  git branch: 'feature1', credentialsId: '4fed7abf-d02c-4ae8-92c2-1f56430fb590', url: 'https://github.com/NARRESHKARRI-DEVOPS/MAVEN--WAR-FILE.git'
	}
	stage('maven'){
	sh "${mavenhome}/bin/mvn clean package"
	}
	stage('sonar'){
	sh "${mavenhome}/bin/mvn clean package sonar:sonar"
	}
	stage('nexus'){
	sh "${mavenhome}/bin/mvn clean deploy sonar:sonar"
	}
	stage('tomcat'){
	sh """
	curl -u admin:hello  \
	--upload-file /var/lib/jenkins/workspace/pipe-line1/target/maven-web-application.war \
     "http://13.233.139.175:8082/manager/text/deploy?path=/maven-web-application&update=true"
    """

	}
} catch (e) {
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

