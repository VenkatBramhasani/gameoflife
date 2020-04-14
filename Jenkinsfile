pipeline {
	agent any
	tools { 
        maven 'maven' 
        jdk 'jdk' 
    }
	triggers{
		pollSCM('* * * * *')
	}
	stages{
		stage('Source Code from GIT ') {  
             steps { 
			 
                echo "Hi Chowdary, welcome to GitHub...!"
                //git credentialsId: 'venkat_gitlab', url: 'http://52.172.194.175:30080/Venkat/simple-gameoflife.git'
                 
				}  
        }
		stage('Maven Build') {
			 steps {
			     //-Dmaven.test.failure.ignore=true (or -DtestFailureIgnore=true) - will ignore any failures occurred during test execution
			     sh 'mvn clean -Dmaven.test.failure.ignore=true install'

			}
			 post {  
           		success {  
					echo 'Maven build success...!'
					junit 'gameoflife-web/target/surefire-reports/**/*.xml'
				}
				failure {
					echo 'Maven build failed....'
				}
			}   
		
		}
		/*stage('SonarQube Analysis') {
			steps{
				withSonarQubeEnv("sonarqube") {
				
					sh "${tool("sonarqube")}/bin/sonar-scanner"
					
					}
					
			}
			post {
				success {
					echo 'SonarQube Analysis...Success..!'  
				}
				failure {
					echo 'SonarQube Analysis...Success..!'
				}
			}
				
		}*/
		stage("Publish to Nexus") {
            steps {
                
                script{

                    def mavenPom = readMavenPom file: 'pom.xml'
                    def nexusRepoName = mavenPom.version.endsWith("SNAPSHOT") ? "venkat-snapshots" : "venkat-releases"
                    nexusArtifactUploader artifacts: [
                        [
                            artifactId: "${mavenPom.artifactId}", 
                            classifier: '', 
                            file: '/var/jenkins_home/workspace/Venkat-Pipeline/gameoflife-web/target/gameoflife.war', 
                            type: 'war'
                        ]
                    ], 
                    credentialsId: 'venkat_nexus', 
                    groupId: "${mavenPom.groupId}", 
                    nexusUrl: '52.172.157.103:58081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: nexusRepoName, 
                    version: "${mavenPom.version}"
                    }
                
            }
			post {
				success {
					echo 'uploading to Nexus reposiotry...Success..!'  
				}
				failure {
					echo 'uploading to Nexus reposiotry...failed..!'
				}
				
			
			}
		
        }
		/*stage('Deploy to Tomcat'){     

		  sshagent(['tomcat']) {

			 sh 'scp -o StrictHostKeyChecking=no gameoflife-web/target/*.war root@192.168.33.12:/opt/tomcat/webapps/'

			}

		}*/

	}
	post{
        always {
            //archive "target/**/*"
            echo 'Hi Chowdary this will always run'
        }
        success {
            //mail to:"me@example.com", subject:"SUCCESS: ${currentBuild.fullDisplayName}", body: "Yay, we passed."
			echo 'This will run only if successful'
        }
        failure {
            //mail to:"me@example.com", subject:"FAILURE: ${currentBuild.fullDisplayName}", body: "Boo, we failed."
			echo 'This will run only if the run was failed'
        }
        unstable {
            //mail to:"me@example.com", subject:"UNSTABLE: ${currentBuild.fullDisplayName}", body: "Huh, we're unstable."
			echo 'This will run only if the run was marked as unstable'
        }
        changed {
            //mail to:"me@example.com", subject:"CHANGED: ${currentBuild.fullDisplayName}", body: "Wow, our status changed!"
			echo 'This will run only if the state of the Pipeline has changed'  
            //For example, if the Pipeline was previously failed but is now successful
        }
    }
		
}
