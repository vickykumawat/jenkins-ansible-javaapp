pipeline {
    agent any

    tools {
        maven "M2"
    }

    stages {
        stage('Build') {
            steps {
                git 'https://github.com/mahendra-shinde/ci-servlet-demo.git'

                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                success {
                    archiveArtifacts 'target/*.war'
                }
            }
        }
        stage('deploy'){
            steps{
                ansiblePlaybook become: true, credentialsId: 'webhostid', installation: 'ansible', inventory: 'deployment/environment.ini', playbook: 'deployment/tomcat.yml'
            }
        }
    }
}
