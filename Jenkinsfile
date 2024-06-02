pipeline {
    agent any
    tools {
        maven "MAVEN3"
        jdk "OracleJDK11"
    }

    stages {
        stage('Fetch Code'){
            steps{
                git branch: 'main', url: 'https://github.com/hkhcoder/vprofile-project.git'
            }
        }
        stage('Build'){
            steps{
                sh 'mvn install -DskipTests'
            }

            post{
                success {
                    echo 'Archiving build artifact'
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage('Test'){
            steps{
                sh 'mvn test'
            }
        }
        stage('Checkstyle Analysis'){
            steps{
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('Sonar Analysis'){
            
            environment{
                    scannerHome = tool 'sonar4.7'
            }
            steps{
                withSonarQubeEnv('sonar') {
                    sh """
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=vprofile \
                        -Dsonar.projectName=vprofile \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sources=src/ \
                        -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                        -Dsonar.junit.reportPaths=target/surefire-reports \
                        -Dsonar.jacoco.reportPaths=target/jacoco.exec \
                        -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml
                    """                   
                }
            }
        }
        stage('Quality Gate'){
            steps{
                timeout(time:5 , unit:'MINUTES'){
                    waitForQualityGate(abortPipeline: true)
                }
            }
        }
        stage('Upload to Nexus') {
            environment {
                NEXUS_URL = '172.23.70.221:8081'
                NEXUS_REPO = 'vprofile-repo'
                NEXUS_CREDENTIALS_ID = 'nexusCredentials'
                ARTIFACT_PATH = 'target/vprofile-v2.war'
            }
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: env.NEXUS_URL,
                    groupId: 'QA',
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                    repository: env.NEXUS_REPO,
                    credentialsId: env.NEXUS_CREDENTIALS_ID,
                    artifacts: [
                        [artifactId: 'vproapp',
                         classifier: '',
                         file: env.ARTIFACT_PATH, 
                         type: 'war']
                    ]
                )
            }
        }
    }
}
