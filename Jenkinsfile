node{

   def mavenhome=tool name: "maven"
	stage('git-clone')
	{
	git branch: 'staging', credentialsId: '9298ec1d-565d-4a96-b33e-a627d60eedd6', url: 'https://github.com/NARRESHKARRI-DEVOPS/MAVEN--WAR-FILE.git'
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
	--upload-file /var/lib/jenkins/workspace/new-pipe-1/target/maven-web-application.war \
	"http://13.234.111.52:8080/manager/text/deploy?path=/maven-web-apllication&update=ture"
	"""
	}
}
