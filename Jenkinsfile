pipeline {
	agent any
	tools {
    	maven 'localMaven'
    	jdk 'localJDK'
    }

	parameters {
		string(name: 'tomcat_dev', defaultValue: '3.17.193.115', description: 'Staging Server')
		string(name: 'tomcat_prod', defaultValue: '52.15.162.76', description: 'Production Server')
	}	

	triggers {
		pollSCM('* * * * *')
	}

	stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
                sh "docker build . -t tomcatwebapp:${env.BUILD_ID}"
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -i /etc/pem/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /etc/pem/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
	}

}