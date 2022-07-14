node
{
  def mavenHome = tool name: "maven3.8.6"
  try{
    notifyBuild('STARTED')
    stage('checkOutCode')
    {
        git branch: 'development', credentialsId: '6ce0c4fd-6891-45dd-b5e4-3fde64c07777', url: 'https://github.com/devops-master-training/maven-web-application.git'
    }
    stage('Build')
    {
       sh "${mavenHome}/bin/mvn clean package"
    }
    stage('ExecuteTestReport')
    {
       sh "${mavenHome}/bin/mvn sonar:sonar"
    }
    stage('deployToArtifact')
    {
       sh "${mavenHome}/bin/mvn deploy"
    }
    stage('deployToTomcat')
    {
      deploy adapters: [tomcat9(credentialsId: '1a28d925-859f-4e4a-be67-6bc0988f8b47', path: '', url: 'http://35.154.179.94:8080/')], contextPath: null, war: '**/maven-web-application12.war'
    }
  }
  
  catch(e)
  {
  currentBuild.result="FAILURE"
  throw e
  }
  finally
  {
    notifyBuild(currentBuild.result)
     #print "*****************"currentBuild.result"*****************"
  }
}

  def notifyBuild(String buildStatus)
  {
  buildStatus=buildStatus?:"SUCCESS"
  def subject="${buildStatus}: JOB ${env.JOB_NAME} #${env.BUILD_NUMBER}"
    def msg="${subject}:\n${BUILD_URL}"
    def color

    if (buildStatus == 'STARTED') {
        color = '#D4DADF'
    } else if (buildStatus == 'SUCCESS') {
        color = '#BDFFC3'
    } else {
        color = 'RED'
    }
     slackSend(color: color, message: msg)
  }
