node
{
    
    def mavenHome = tool name: "maven3.8.5" 

    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

    echo "Job Name is :- ${env.JOB_NAME} "
    echo "Node name is :-  ${env.NODE_NAME} "
    echo "Build Number is :-  ${env.BUILD_NUMBER} "   
    
    try
    {    
       
        stage ('CheckoutCode')
        {
            git branch: 'development', credentialsId: '308e168c-bb4a-4ec2-835e-b30d5305ad80', url: 'https://github.com/sudeshsonawane/maven-web-application.git'
        }

        stage('Build')
        {
            sh "${mavenHome}/bin/mvn clean package"
        }

        stage('ExecuteSonarQubeReport')
        {
            sh "${mavenHome}/bin/mvn sonar:sonar"
        }

        stage('UploadArtifactIntoNexus')
        {
            sh "${mavenHome}/bin/mvn clean deploy"
        }

        stage('DeplyApplicationIntoTomcatServer')
        {
            sshagent(['a200758e-6add-4264-87fb-92f18560b887'])
            {
                sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.32.185:/opt/apache-tomcat-9.0.63/webapps/"
            }
        }
    }//try closing
   
    catch(e)
    {
        currentBuild.result = "FAILED"
        throw e
    }
    
    finally
    {
        sendSlackNotifications(currentBuild.result)
    }
            
}//Node closing

// Below code will be used for sending slack notification.

def sendSlackNotifications(String buildStatus = 'STARTED') {
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

