node{
    
def mavenHome = tool name: "maven3.8.5" 

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

stage ('CheckoutCode')
{
git branch: 'development', credentialsId: '308e168c-bb4a-4ec2-835e-b30d5305ad80', url: 'https://github.com/sudeshsonawane/maven-web-application.git'
}

stage('Build')
{
sh "${mavenHome}/bin/mvn clean package"
}
/*
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

*/
}
