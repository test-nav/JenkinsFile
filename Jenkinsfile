node()
{
	def mavenHome = tool name : "maven3.6.3"
	
	properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
	
	stage('FitHubCodeCheckOut')
	{
		git branch: 'development', credentialsId: 'b5a64436-c87f-4d5d-8dcf-fcfaee58a068', url: 'https://github.com/test-nav/maven-web-application.git'
	}
	
	stage('BuildPackage')
	{
			sh "${mavenHome}/bin/mvn clean package"
	}
	
	stage('ExecuteSonarQubeReport')
	{
		sh "${mavenHome}/bin/mvn sonar:sonar"
	}
	
	stage('UploadArtifactIntoNexus')
	{
		sh "${mavenHome}/bin/mvn deploy"
	}
	
    stage('ApplicationDeployment')
	{
		deploy adapters: [tomcat9(credentialsId: '1866d957-bce9-40d9-95e2-beaf815dc63d', path: '', url: 'http://192.168.9.131:7070/')], contextPath: null, war: '**/maven-web-application.war'
	}
	
	stage('SendMailNotification')
	{
		emailext body: '''

		Thanks & Regards,
		Naveen Kumar Katakam''', compressLog: true, replyTo: 'navien.kumar@gmail.com', subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', to: 'katakamnavienk@gmail.com'
	}
}
