# End to End CI/CD Pipeline Implementation using Jenkins

In this project we will take the code from GitHub, build the code using maven, perform unit test and code analysis using Checkstyle and SonarQube server, and finally store the artifact in the Nexus Repository server. 
We will build the dockerImage using the dockerfile and store the image to Amazon ECR repo.
Deploy the application using Amazon ECS service.

### Steps
---
#### Jenkins setup
- Set up Jenkins master server on the AWS and provision it with the `jenkins_setup.sh` file.
- Jenkins password is located in **/var/lib/jenkins/secrets/initialAdminPassword**

#### Nexus setup
- Set up Nexus repository server on the AWS and provision it with the `nexus_setup.sh` file.
- Nexus login credentials:
  username: **admin**
  password is located in **/opt/nexus/sonatype-work/nexus3/admin.password** 

#### SonarQube setup
- Setup SonarQube repository server on the AWS and provision it with the `sonarqube_setup.sh` file
- username and password are **admin** and **admin** respectively. 

#### Setup security groups for these instances
- Port for Jenkins : 8080
- Port for Nexus repository server: 8081
- Port for SonarQube server: 80

#### Required Plugins
- Nexus Artifact Uploader
- SonarQube scanner
- Git
- Pipeline Maven Integration 
- BuildTimestamp
- Pipeline Utility Steps
- Docker pipeline
- Cloudbees Docker Build and Publish
- Amazon ECR
- Amazon web service SDK
- Pipeline: AWS steps plugin

#### Install required tools
- Maven
- OracleJDK-11
- Sonar scanner

#### Credentials
- GitHub SSH credentials
- Nexus repository credentials using access token

#### Configure
- Configure sonar server in the Jenkins settings
- Setup github webhook for the vprofile repository, `http://<jenkins-server-IP>:8080/github-webhook/`
- Setup webhook for quality gate in sonarqube server, `http://<jenkins-server-privateIP>:8080/sonarqube-webhook`
- Install Docker engine on Jenkins Instance
- Add `jenkins` user to `docker` group and reboot
- Install `awscli` on Jenkins Instance

#### Create IAM user with two policies
- `AmazonEC2ContainerRegistryFullAccess`
- `AmazonECS_FullAccess` 
Then store the aws credentials in jenkins

---

This application is made my visualpath.
# Prerequisites
- JDK 11 
- Maven 3 
- MySQL 8

# Technologies 
- Spring MVC
- Spring Security
- Spring Data JPA
- Maven
- JSP
- Tomcat
- MySQL
- Memcached
- Rabbitmq

# Database
Here, we used Mysql DB 
sql dump file:
- /src/main/resources/db_backup.sql
- db_backup.sql file is a mysql dump file. We have to import this dump to mysql db server
- ` > mysql -u <user_name> -p accounts < db_backup.sql `





