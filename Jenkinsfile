pipeline {
    agent { label 'master' }
    parameters {
        choice(name: 'BRANCH', choices: ['master', 'sprint2', 'dev'], description: 'Select the branch to build')
    }
    environment { 
        PATH="/opt/apachemaven/bin:$PATH"
    }
    stages {
        stage('SCM Checkout') {
            steps {
                git branch: "${params.BRANCH}", credentialsId: 'newgit', url: 'https://github.com/lokasanimamatha/hello-world.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install package'
            }
        }
        stage('Push Artifact to Nexus') {
            steps {
                sh 'curl --user admin:admin123 --upload-file webapp/target/webapp.war http://3.228.7.207:8081/repository/ossapp/'
            }
        }
        stage('Deploy') {
           agent { label 'devserver' } 
            steps {
                sh '''cd /opt/tomcat/webapps/
                rm -rf webapps.*
                wget http://3.228.7.207:8081/repository/ossapp/webapp.war
                cd /opt/tomcat/bin/
                ./shutdown.sh
                sleep 20
                ./startup.sh'''
            }
        }
    }
}
