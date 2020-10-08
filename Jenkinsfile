pipeline
{
	agent any
	
	tools
	{
		maven 'Maven_3.6.3'
    	}
	
	triggers
	{
		pollSCM('* * * * *')
	}
	
	options 
	{
		timestamps()
	}
	
	stages
	{
		stage('Git Checkout')
		{
			steps
			{
				git branch: 'development', url: 'https://github.com/PavanReddy77/maven-web-application.git'
				echo "Source code is Successfully Pulled from GitHub"
            		}
        	}
		stage('Build')
		{
            		steps
			{
				echo "Build is Started"
				sh 'mvn clean package'
				echo "Build is Successful"
            		}
        	}
		stage('SonarQube and Nexus')
		{
			parallel
			{
				stage('Sonarqube Test')
				{
					steps
					{
						echo "SonarQube Test is Started"
						sh 'mvn sonar:sonar'
						echo "SonarQube Test is Successful"
					}
				}
				stage('Upload Artifact')
				{
					steps
					{
						sh 'mvn deploy'
						echo "Artifact is Successfully Uploaded into Nexus"
					}
				}
			}
		}
		stage('Deploy Tomcat')
		{
			steps
			{
				sshagent(['36070746-b68c-4b2d-a603-2f512f91f85d']) 
				{
					sh 'scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.232.191.132:/opt/apache-tomcat-9.0.38/webapps/'
					echo "Application is Successfully Deployed into Tomcat Server"
				}
			}
		}
		stage('Notifications')
		{
			parallel
			{
				stage('Slack')
				{
					steps
					{
						slackSend channel: 'test-automation',
						color: 'good',
						message: "Application is Successfully Deployed into Production Server\n ${currentBuild.currentResult}:* Job Name: ${env.JOB_NAME} || Build Number: ${env.BUILD_NUMBER}\n More information at: ${env.BUILD_URL}"
					}
				}
				stage('Gmail')
				{
					steps
					{
						emailext body: "Application is Successfully Deployed into Production Server\n${currentBuild.currentResult}:* Job Name: ${env.JOB_NAME} || Build Number: ${env.BUILD_NUMBER}\nMore information at: ${env.BUILD_URL}",
						subject: 'CICD Pipeline Build Status',
						to: 'TestAutomationDevOps@gmail.com'
					}
				}
			}
		}
	}
	post
	{
		always
		{
			cleanWs()
        	}
		failure
		{
			echo 'This Job is Failed - Notifications have been sent to Slack and Gmail..!!'
		
			slackSend channel: 'test-automation',
			color: 'good',
			message: "Application is Successfully Deployed into Production Server\n ${currentBuild.currentResult}:* Job Name: ${env.JOB_NAME} || Build Number: ${env.BUILD_NUMBER}\n More information at: ${env.BUILD_URL}"
		
			emailext body: "Application is Successfully Deployed into Production Server\n${currentBuild.currentResult}:* Job Name: ${env.JOB_NAME} || Build Number: ${env.BUILD_NUMBER}\nMore information at: ${env.BUILD_URL}",
			subject: 'CICD Pipeline Build Status',
			to: 'TestAutomationDevOps@gmail.com'
		}
	}
}
