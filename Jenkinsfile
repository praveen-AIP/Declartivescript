pipeline
{
	agent any
	
	tools
	{
		maven 'maven-3.6.3'
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
				git branch: 'development', credentialsId: '74bac532-9fbe-41a0-ad03-4e0fb5afbdcb', url: 'https://github.com/praveen-AIP/maven-web-application.git'
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
		        sshagent(['TomcatCrdentials']) 
		            {
					        sh 'scp -o StrictHostKeyChecking=no target/maven-web-application.war praveen.k.polepalli@35.193.93.148:/opt/apache-tomcat-9.0.38/webapps/'
					        echo "Application is Successfully Deployed into Tomcat Server"
				    }
		       	}
	   }
	   stage('Notifications mail')
		{
		    steps
			{
						emailext body: "Application is Successfully Deployed into Production Server\n${currentBuild.currentResult}:* Job Name: ${env.JOB_NAME} || Build Number: ${env.BUILD_NUMBER}\nMore information at: ${env.BUILD_URL}",
						subject: 'CICD Pipeline Build Status',
						to: 'polepalli514@hotmail.com'
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
			echo 'This Job is Failed - Notifications have been sent to Gmail..!!'
		
			emailext body: "Application is Successfully Deployed into Production Server\n${currentBuild.currentResult}:* Job Name: ${env.JOB_NAME} || Build Number: ${env.BUILD_NUMBER}\nMore information at: ${env.BUILD_URL}",
			subject: 'CICD Pipeline Build Status',
			to: 'polepalli514@gmail.com'
		}
	}
    	
	
}
