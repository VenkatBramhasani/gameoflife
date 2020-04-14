node{
	properties(
		[
			pipelineTriggers(
				[
					pollSCM('* * * * *')
				]
			),
		]
	
	)
	timestamps{
    stage('SCM Checkout'){
			  git 'http://52.172.157.103:30080/venkat/gameoflife.git'
			  //git credentialsId: 'venkat_gitlab', url: 'http://52.172.157.103:30080/venkat/gameoflife.git'
    }
    stage('Compile-Package'){
			  def mvnHome = tool name: 'maven', type: 'maven'
			  def mvnCMD = "${mvnHome}/bin/mvn"
			  sh "${mvnCMD} clean package"
			  junit 'gameoflife-web/target/surefire-reports/**/*.xml'
			  currentBuild.result = 'SUCCESS'
    }
	if(currentBuild.result != 'UNSTABLE'){
		stage('SonarQube Analysis') {
				def mvnHome = tool name: 'maven', type: 'maven'
				def mvnCMD = "${mvnHome}/bin/mvn"
				withSonarQubeEnv('sonarqube') { 
				  sh "${mvnCMD} sonar:sonar " +
				  '-Dsonar.projectKey=${JOB_NAME} ' +
				  '-Dsonar.projectName=${JOB_NAME} '+
				  '-Dsonar.projectVersion=${BUILD_NUMBER} ' 
				}
		}
	}
	stage('NexusDeploy') {
            nexusPublisher nexusInstanceId: 'Nexus_Local', \
            nexusRepositoryId: 'venkat-releases', \
            packages: [[$class: 'MavenPackage', \
            mavenAssetList: [[classifier: '', extension: '', filePath: '/var/jenkins_home/workspace/venkat_poc/gameoflife-web/target/gameoflife.war']], \
            mavenCoordinate: [artifactId: 'gameoflife', \
            groupId: '${JOB_NAME}', \
            packaging: 'war', \
            version: '${BUILD_NUMBER}']]]
    }
	stage('Deploy to Tomcat'){
	
			echo " Hi Chowdary,Tomcat deployment succeeded...!"

			/*sshagent(['tomcat']) {			

				sh 'scp -o StrictHostKeyChecking=no gameoflife-web/target/*.war root@192.168.33.12:/opt/tomcat/webapps/'
			}*/

		}
	}
}

