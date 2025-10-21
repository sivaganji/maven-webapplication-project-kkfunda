node {
    try
    {
      notifyBuild()  
      def mavenHome =  tool name: 'Maven-3.9.9'
    stage('git checkout')
    {
      git 'https://github.com/sivaganji/maven-webapplication-project-kkfunda.git'
    }
    stage('Build') 
    {
      sh "${mavenHome}/bin/mvn clean package"
    }
   /* stage('SonarQube  Report')
   {
   sh "${mavenHome}/bin/mvn sonar:sonar"
   }*/
   stage('Upload to Nexus') {
    sh "${mavenHome}/bin/mvn deploy"
    }
  stage('Deploy to Tomcat') {
    echo "Deploying WAR file using curl..."

    sh """
        curl -u siva:Sivasankarreddy#123 \
        --upload-file /var/lib/jenkins/workspace/scriptedpipeline/target/maven-web-application.war \
        "http://18.188.111.18:8080/manager/text/deploy?path=/maven-web-application&update=true"
    """
   }
}catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
    throw e
  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
}

def notifyBuild(String buildStatus = 'STARTED') {
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
