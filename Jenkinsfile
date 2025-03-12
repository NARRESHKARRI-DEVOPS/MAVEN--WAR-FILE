node{

   def mavenhome=tool name: "maven"
	stage('git-clone')
	{
	 git branch: 'feature1', credentialsId: '4fed7abf-d02c-4ae8-92c2-1f56430fb590', url: 'https://github.com/NARRESHKARRI-DEVOPS/MAVEN--WAR-FILE.git'
	}
	stage('maven')
	{
    sh "${mavenhome}/bin/mvn clean package"
	}
	stage('sonar')
	{
	sh "${mavenhome}/bin/mvn clean package  sonar:sonar"
	}
	stage ('nexus')
	{
	sh "${mavenhome}/bin/mvn clean sonar:sonar deploy"
	}
	stage('deploy')
	{
	sh """
	curl -u admin:hello \
	--upload-file /var/lib/jenkins/workspace/multiwithscripting/target/maven-web-application.war \
	"http://13.233.139.175:8082/manager/text/deploy?path=/maven-web-apllication&update=ture"
	"""
	}
}
