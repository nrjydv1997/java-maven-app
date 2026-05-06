pipeline {
    agent any

    tools {
        maven "Maven3"
    }

    stages {
        
        stage('Clean WS') {
            steps {
                cleanWs()
            }
        }
        
        stage('Git Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/nrjydv1997/java-maven-app.git'
            }
        } 
        
        stage('Maven build') {
            steps {
                sh 'mvn clean package'
            }
        } 
        
        stage('Docker Image build and push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh '''
                        docker build -t javamavenapp .
                        docker tag javamavenapp nrjydv1997/javamavenapp:latest
                        docker push nrjydv1997/javamavenapp:latest
                        '''
                    }
                }
            }
        }
        
        stage('Remove Docker container') {
            steps {
                sh 'docker rm -f java_maven_app || true'
            }
        }
        
        stage('Run Docker container') {
            steps {
                sh 'docker run -d -p 9000:8080 --name java_maven_app nrjydv1997/javamavenapp:latest'
            }
        }
    }

    
    post {
        always {
            script {
                def buildStatus = currentBuild.currentResult
                def buildUser = currentBuild.getBuildCauses('hudson.model.Cause$UserIdCause')[0]?.userId ?: 'Github User'
                
                emailext (
                    subject: "Pipeline ${buildStatus}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: """
                        <p>This is a java-maven-app CICD pipeline status.</p>
                        <p>Project: ${env.JOB_NAME}</p>
                        <p>Build Number: ${env.BUILD_NUMBER}</p>
                        <p>Build Status: ${buildStatus}</p>
                        <p>Started by: ${buildUser}</p>
                        <p>Build URL: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    """,
                    to: 'nrjydv1997@gmail.com',
                    from: 'nrjydv1997@gmail.com',
                    replyTo: 'nrjydv1997@gmail.com',
                    mimeType: 'text/html',
                    attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
                )
            }
        }
    }
}
