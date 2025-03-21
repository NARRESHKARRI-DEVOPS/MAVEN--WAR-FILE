pipeline {
    agent any 
    tools {
        maven "maven"
    }

    stages {
        stage('Checkout Stage') { 
            steps {
                git branch: 'feature1', credentialsId: '4fed7abf-d02c-4ae8-92c2-1f56430fb590', url: 'https://github.com/NARRESHKARRI-DEVOPS/MAVEN--WAR-FILE.git'
            }
        }
        
        stage('Build') { 
            steps {
                sh "mvn clean package"
            }
        }
        
        stage('SQREPORT') {
            steps {
                sh "mvn sonar:sonar"
            }
        }
        
        stage('Deploy to Nexus') {
            steps {
                sh "mvn deploy"
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    sshagent(['plugin']) {
                        // Stop Tomcat service gracefully
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@13.233.139.175 'sudo /opt/tomcat/bin/shutdown.sh'"
                        
                        // Deploy WAR file to Tomcat
                        sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.233.139.175:/opt/tomcat/webapps/"

                        // Permissions on the target server
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@13.233.139.175 'sudo chown -R ec2-user:ec2-user /opt/tomcat/webapps/'"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@13.233.139.175 'sudo chmod -R 777 /opt/tomcat/webapps/'"
                        
                        // Start Tomcat service
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@13.233.139.175 'sudo /opt/tomcat/bin/startup.sh'"
                    }
                }
            }
        }
    } // Stages closing

    post {
        always {
            script {
                notifyBuild('STARTED')  // 🔹 Sends notification at the beginning
            }
        }
        success {
            script {
                notifyBuild(currentBuild.result)
            }
        }
        failure {
            script {
                notifyBuild(currentBuild.result)
            }
        }
    }
} // Pipeline closing

// ✅ Fixed Notification Function
void notifyBuild(String buildStatus) {
    buildStatus = buildStatus ?: 'SUCCESS'

    def colorName = 'RED'
    def colorCode = '#FF0000'
    def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
    def summary = "${subject} (${env.BUILD_URL})"

    if (buildStatus == 'STARTED') {
        colorName = 'YELLOW'
        colorCode = '#FFFF00'
    } else if (buildStatus == 'SUCCESS') {
        colorName = 'GREEN'
        colorCode = '#00FF00'
    }

    // List of Slack channels to notify
    def slackChannels = ['#hello', '#veera101502', '#sla']

    // Send notifications to all channels
    for (channel in slackChannels) {
        slackSend(color: colorCode, message: summary, channel: channel)
    }
}

