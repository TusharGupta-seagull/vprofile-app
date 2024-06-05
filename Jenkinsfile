pipeline {
    agent any
    tools {
        maven "MAVEN3"
        jdk "OracleJDK11"
    }
    environment{
        registryCredential = 'ecr:us-east-1:awscreds'
        appRegistry = "932620631845.dkr.ecr.us-east-1.amazonaws.com/vprofileimg-repo"
        vprofileRegistry = "http://932620631845.dkr.ecr.us-east-1.amazonaws.com"
        cluster = "vprofile"
        service = "vproappsvc"
    }
    stages {
        // stage('Fetch Code'){
        //     steps{
        //         git branch: 'main', url: 'https://github.com/hkhcoder/vprofile-project.git'
        //     }
        // }
        stage('Build'){
            steps{
                sh 'mvn install -DskipTests'
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
        stage('Build App Imgae'){
            steps{
                script{
                    dockerImage = docker.build(appRegistry + ":$BUILD_NUMBER", "./Docker-file/")
                }
            }
        }
        stage('Upload App Image'){
            steps{
                script{
                    docker.withRegistry(vprofileRegistry, registryCredential){
                        dockerImage.push("$BUILD_NUMBER")
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage('Deploy to ecs'){
            steps{
                withAWS(credentials: 'awscreds', region: 'us-east-1'){
                    sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
                }
            }
        }
    }
}
