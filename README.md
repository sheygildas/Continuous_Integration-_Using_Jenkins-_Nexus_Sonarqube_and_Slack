
# Project-03
## :ledger: Index

- [About The Project](#beginner-about-the-project)
- [Problem that this project solves ](#question-problem-that-this-project-solves)
- [Solution to the problem.](#key-solution-to-the-problem)
- [Tools and Services](#hammer_and_wrench-tools-and-services)
- [Architecture of this project](#house-architecture-of-this-project)
- [Steps to execute the project](#zap-steps-to-execute-the-project)
  - [Login to AWS Account ](#key-login-to-aws-account )
  - [Create Key Pairs](#closed_lock_with_key-create-key-pairs)
  - [Create Security groups](#lock-create-security-groups)
    - [Jenkins](#lock-jenkins)
    - [Nexus](#lock-nexus)
    - [SonarQube](#lock-sonarqube)
  - [Launch Instances with user data](#bulb-launch-instances-with-user-data )
    - [Jenkins](#lock-jenkins)
    - [Nexus](#lock-nexus)
    - [SonarQube](#lock-sonarqube)
  - [Jenkins Post Installation](#earth_africa-jenkins-post-installation)
  - [Nexus Post Installation](#earth_africa-nexus-post-installation)
  - [Build Job](#hammer_and_wrench-build-job)
  - [Setup Slack Notification  ](#rocket-setup-slack-notification )
  - [Checkstyle code analysis Job](#package-checkstyle-code-analysis-job)
  - [Setup Sonar integration ](#lock-setup-sonar-integration)
  - [Sonar code analysis job](#earth_africa-sonar-code-analysis-job)
  - [Artifact upload job](#hammer_and_wrench-artifact-upload-job)
  - [Connect-all-jobs-together-with-Build-Pipeline](#hammer_and_wrench-connect-all-jobs-together-with-build-pipeline)
  - [Set Automatic build trigger](#hammer_and_wrench-set-automatic-build-trigger)
  - [Make Code change](#hammer_and_wrench-make-code-change)
- [Verify from browser](#earth_africa-verify-from-browser) 
- [Resources](#page_facing_up-resources)
- [Credit/Acknowledgment](#star2-creditacknowledgment)


## :beginner:About The Project

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

## :question: Problem that this project solves 

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

## :key: Solution to the problem.

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

## :hammer_and_wrench: Tools and Services
- visual studio code or an IDE
- AWS Account
- Nexus
- SonarQube
- Slack
- Maven
- JDK8
- Github Account

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>


## :beginner: Architecture of this project.

![Project Image](project-image-url)

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

## :zap: Steps to Execute the project. 

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

### :key: Login to AWS Account
- Login to your AWS management console. Create an AWS account if you don't have one. 

 ```sh
Account ID (12 digits) or account alias: <Enter your credentials>
IAM user name: <Your IAM user name>
Password: <Your password>
   ```
   
<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

### :closed_lock_with_key: Create Key Pairs

- On the console, search for `EC2` scrol down and select `key pair`-> `create key pair`.
- Create the  key pair that you use to login to your AWS services and name it as follows. This key will be used for all our instances.

 ```sh
Name: ci-vprofile-key
   ```

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

### :lock: Create Security groups

- Create three security group for the Jenkins,Nexux and Sonaqube Instancse.

#### :lock: Jenkins

- On your console under `EC2`->`security group` click `create security group`.
- Create a security group for the `Jenkins server` with the following details.

 ```sh
Name: vprofile-jenkins-sg
Description: vprofile-jenkins-sg
Tag: vprofile-jenkins-sg
Allow: 8080 from MY IP 
Allow: 22 from Anywhere from MY IP
Allow: 8080 from vprofile-sonarqube-sg
   ```

#### :lock: Nexus

- On your console under `EC2`->`security group` click `create security group`.
- Create a security group for the `Nexus server` with the following details.

 ```sh
Name: vprofile-nexus-sg
Description: vprofile-nexus-sg
Tag:  vprofile-nexus-sg
Allow: 8081 from MY IP
Allow: 22 from Anywhere from MY IP
Allow: 8081 from vprofile-jenkins-sg
   ```

#### :lock: SonarQube


- On your console under `EC2`->`security group` click `create security group`.
- Create a security group for the `SonQube server` with the following details.

 ```sh
Name: vprofile-sonarqube-sg
Description: vprofile-sonarqube-sg
Tag: vprofile-sonarqube-sg
Allow: 80 from MY IP
Allow: 80 from vprofile-jenkins-sg
   ```
   
<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

### :bulb: Launch Instances with user data 

- Now let's create three EC2 instances and launch Instances with user data
- 
#### :bulb: Jenkins

- On your console under `EC2`->`Instances` click `launch instances`.
- We will create Jenkins-server with below details.

```sh
Name: jenkins-server
AMI: Ubuntu 20.04
SecGrp: vprofile-jenkins-sg
InstanceType: t2.small
KeyPair: ci-vprofile-key
   ```
- Create the script to provision our `Jenkins server` with the Userdata script below.


```sh
#!/bin/bash
sudo apt update
sudo apt install openjdk-11-jdk -y
sudo apt install maven -y
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins -y
###
   ```

#### :bulb: SonarQube

- On your console under `EC2`->`Instances` click `launch instances`.
- We will create SonarQube-server with below details.

```sh
Name: sonar-server
AMI: Ubuntu 18.04
InstanceType: t2.medium
SecGrp: vprofile-sonar-sg
KeyPair: ci-vprofile-key
   ```

- Create the script to provision our `SonarQube server` with the Userdata script below.


```sh
#!/bin/bash
cp /etc/sysctl.conf /root/sysctl.conf_backup
cat <<EOT> /etc/sysctl.conf
vm.max_map_count=262144
fs.file-max=65536
ulimit -n 65536
ulimit -u 4096
EOT
cp /etc/security/limits.conf /root/sec_limit.conf_backup
cat <<EOT> /etc/security/limits.conf
sonarqube   -   nofile   65536
sonarqube   -   nproc    409
EOT

sudo apt-get update -y
sudo apt-get install openjdk-11-jdk -y
sudo update-alternatives --config java

java -version

sudo apt update
wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -

sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
sudo apt install postgresql postgresql-contrib -y
#sudo -u postgres psql -c "SELECT version();"
sudo systemctl enable postgresql.service
sudo systemctl start  postgresql.service
sudo echo "postgres:admin123" | chpasswd
runuser -l postgres -c "createuser sonar"
sudo -i -u postgres psql -c "ALTER USER sonar WITH ENCRYPTED PASSWORD 'admin123';"
sudo -i -u postgres psql -c "CREATE DATABASE sonarqube OWNER sonar;"
sudo -i -u postgres psql -c "GRANT ALL PRIVILEGES ON DATABASE sonarqube to sonar;"
systemctl restart  postgresql
#systemctl status -l   postgresql
netstat -tulpena | grep postgres
sudo mkdir -p /sonarqube/
cd /sonarqube/
sudo curl -O https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.3.0.34182.zip
sudo apt-get install zip -y
sudo unzip -o sonarqube-8.3.0.34182.zip -d /opt/
sudo mv /opt/sonarqube-8.3.0.34182/ /opt/sonarqube
sudo groupadd sonar
sudo useradd -c "SonarQube - User" -d /opt/sonarqube/ -g sonar sonar
sudo chown sonar:sonar /opt/sonarqube/ -R
cp /opt/sonarqube/conf/sonar.properties /root/sonar.properties_backup
cat <<EOT> /opt/sonarqube/conf/sonar.properties
sonar.jdbc.username=sonar
sonar.jdbc.password=admin123
sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube
sonar.web.host=0.0.0.0
sonar.web.port=9000
sonar.web.javaAdditionalOpts=-server
sonar.search.javaOpts=-Xmx512m -Xms512m -XX:+HeapDumpOnOutOfMemoryError
sonar.log.level=INFO
sonar.path.logs=logs
EOT

cat <<EOT> /etc/systemd/system/sonarqube.service
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

User=sonar
Group=sonar
Restart=always

LimitNOFILE=65536
LimitNPROC=4096


[Install]
WantedBy=multi-user.target
EOT

systemctl daemon-reload
systemctl enable sonarqube.service
#systemctl start sonarqube.service
#systemctl status -l sonarqube.service
apt-get install nginx -y
rm -rf /etc/nginx/sites-enabled/default
rm -rf /etc/nginx/sites-available/default
cat <<EOT> /etc/nginx/sites-available/sonarqube
server{
    listen      80;
    server_name sonarqube.groophy.in;

    access_log  /var/log/nginx/sonar.access.log;
    error_log   /var/log/nginx/sonar.error.log;

    proxy_buffers 16 64k;
    proxy_buffer_size 128k;

    location / {
        proxy_pass  http://127.0.0.1:9000;
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
        proxy_redirect off;
              
        proxy_set_header    Host            \$host;
        proxy_set_header    X-Real-IP       \$remote_addr;
        proxy_set_header    X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header    X-Forwarded-Proto http;
    }
}
EOT
ln -s /etc/nginx/sites-available/sonarqube /etc/nginx/sites-enabled/sonarqube
systemctl enable nginx.service
#systemctl restart nginx.service
sudo ufw allow 80,9000,9001/tcp

echo "System reboot in 30 sec"
sleep 30
reboot
###
   ```
   
#### :bulb: Nexus

- On your console under `EC2`->`Instances` click `launch instances`.
- We will create Nexus-server with below details.

```sh
Name: nexus-server
AMI: Amazon Linux-2
InstanceType: t2.medium
SecGrp: vprofile-nexus-sg
KeyPair: vprofile-ci-key
KeyPair: ci-vprofile-key
   ```
- Create the script to provision our `Nexus server` with the Userdata script below.


```sh
#!/bin/bash
yum install java-1.8.0-openjdk.x86_64 wget -y   
mkdir -p /opt/nexus/   
mkdir -p /tmp/nexus/                           
cd /tmp/nexus/
NEXUSURL="https://download.sonatype.com/nexus/3/latest-unix.tar.gz"
wget $NEXUSURL -O nexus.tar.gz
EXTOUT=`tar xzvf nexus.tar.gz`
NEXUSDIR=`echo $EXTOUT | cut -d '/' -f1`
rm -rf /tmp/nexus/nexus.tar.gz
rsync -avzh /tmp/nexus/ /opt/nexus/
useradd nexus
chown -R nexus.nexus /opt/nexus 
cat <<EOT>> /etc/systemd/system/nexus.service
[Unit]                                                                          
Description=nexus service                                                       
After=network.target                                                            
                                                                  
[Service]                                                                       
Type=forking                                                                    
LimitNOFILE=65536                                                               
ExecStart=/opt/nexus/$NEXUSDIR/bin/nexus start                                  
ExecStop=/opt/nexus/$NEXUSDIR/bin/nexus stop                                    
User=nexus                                                                      
Restart=on-abort                                                                
                                                                  
[Install]                                                                       
WantedBy=multi-user.target                                                      
EOT
echo 'run_as_user="nexus"' > /opt/nexus/$NEXUSDIR/bin/nexus.rc
systemctl daemon-reload
systemctl start nexus
systemctl enable nexusSonarQube Server Setup
   ```

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

### :earth_africa: Jenkins Post Installation

-  Let's `SSH` into our `jenkins server` and check the status os Jenkins, let's also view the content of `/var/lib/jenkins/secrets/initialAdminPassword`

```sh
sudo -i /Doundloads/ci-viprofile-key.pem ubuntu@your Jenkins IP
systemctl status jenkins
cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

Go to browser, http://<public_ip_of_jenkins_server>:8080, enter initial Admin Passwrd and install suggested plugins and additional plugins. Then create your first admin user.

```sh
Maven Integration
Github Integration
Nexus Artifact Uploader
SonarQube Scanner
Slack Notification
Build Timestamp
   ```

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

### :earth_africa: Nexus Post Installation


- Login with SSH into nexus server and check system status for nexus also view the password at `/opt/nexus/sonatype-work/nexus3/admin.password`


```sh
sudo -i /Doundloads/ci-viprofile-key.pem ubuntu@your Nexus IP
systemctl status jenkins
cat /opt/nexus/sonatype-work/nexus3/admin.password
   ```
- Go to browser, http://<public_ip_of_nexus_server>:8081 , enter Username as `admin`, paste password from previous step, click on sign-in. Setup our `new password` and select Disable Anonymous Access.


- Let's create a repository that will be used to store our release artifacts with the following details.


```sh
maven2 hosted
Name: vprofile-release
Version policy: Release
   ```

- create a maven2 proxy repository which will store our dependencies. We will downdload our dependencies from this repository when ever we need them in our project

```sh
maven2 proxy
Name: vpro-maven-central
remote storage: https://repo1.maven.org/maven2/
   ```

- Create another repository which  will be used to store our snapshot artifacts.  any artifact with `-SNAPSHOT` extension will be stored in this repository

```sh
maven2 hosted
Name: vprofile-snapshot
Version policy: Snapshot
   ```
Finally, create a repository for `maven2 group type`. We will use this repository to group all maven repositories.

```sh
maven2 group
Name: vpro-maven-group
Member repositories: 
 - vpro-maven-central
 - vprofile-release
 - vprofile-snapshot
   ```

### :hammer_and_wrench: Build Job

-  Build the Artifact from our source code using Maven. Before our artifact, configure Maven and JDK8 in jenkins.

- Install JDK8 on your server 

```sh
sudo apt update -y
sudo apt install openjdk-8-jdk -y
sudo -i
ls /usr/lib/jvm
### we should get both jdk-11 and jdk-8 in this path ###
java-1.11.0-openjdk-amd64  java-11-openjdk-amd64  openjdk-11
java-1.8.0-openjdk-amd64   java-8-openjdk-amd64
   ```

- Go to `Manage Jenkins`-> `Global Tool Configuration` configure the Installed `JDK8` manually, and specify its PATH `/usr/lib/jvm/java-8-openjdk-amd64' .

   
   
-- Add Nexus login credentials to Jenkins. Go to  ` Manage Jenkins`-> `Manage Credentials`-> `Global` -> `Add Credentials` 
 
 ```sh
username: admin
password: <pwd_setup_for_nexus>
ID: nexuslogin
description: nexuslogin
   ```
 - Create Jenkinsfile to build pipeline, code below
 - 
 ```sh
pipeline {
    agent any
    tools {
        maven "MAVEN3"
        jdk "OracleJDK8"
    }
    environment {
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'admin'
        RELEASE_REPO = 'vprofile-release'
        CENTRAL_REPO = 'vpro-maven-central'
        NEXUSIP = '172.31.10.139'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vpro-maven-group'
        NEXUS_LOGIN = 'nexuslogin'
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }
        }
    }
}
   ```
   
 - Create a New Job in Jenkins with properties below.
```sh
Pipeline from SCM 
Git
URL: <url_from_project> I will use SSH link
Crdentials: we will create github login credentials
#### add Jenkins credentials for github ####
Kind: SSH Username with private key
ID: githublogin
Description: githublogin
Username: git
Private key file: paste your private key here
#####
Branch: */ci-jenkins
path: Jenkinsfile
   ```


- Login jenkins server via SSH and complete host-key checking step. with command below, our host-key will be stored in .ssh/known_hosts file. Only then will error be fixed

 - Create a New Job in Jenkins with properties below.
 
```sh
sudo -i
sudo su - jenkins
git ls-remote -h -- git@github.com:Osomudeya/vpro-ci-jenkin.git HEAD
#####
Branch: */ci-jenkins
path: Jenkinsfile
   ```
- Now Build our project . Our build pipeline is successful


<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

### :rocket: Setup Slack Notification

- Login to `slack` and create a` workspace` by following the prompts. Then create a channel `jenkins-cicd` in our workspace

- Add jenkins to slack. 

- Go to Jenkins dashboard  `Configure system` -> `Slack`

 
```sh
Workspace:  example (in the workspace url example.slack.com)
credential: slacktoken 
default channel: #jenkins-cicd
   ```

- Add our sonar token to global credentials.

 
```sh
Kind: secret text
Secret: <paste_token>
name: slacktoken
description: slacktoken
   ```

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

### :package: Checkstyle code analysis Job

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

### :lock: Setup Sonar integration

-  Go to `Manage Jenkin`s -> `Global Tool Configuration` and configure Sonaqube


```sh
Add sonar scanner
name: sonarscanner
tick install automatically
   ```
- Next we need to go to Configure System, and find SonarQube servers section

```sh
tick environment variables
Add sonarqube
Name: sonarserver
Server URL: http://<private_ip_of_sonar_server>
Server authentication token: we need to create token from sonar website
   ```
   
 Add sonar token to global credentials.
 

```sh
Kind: secret text
Secret: <paste_token>
name: sonartoken
description: sonartoken
   ``` 
<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

### :earth_africa: Sonar code analysis job

- create a SonarQube code for our pipeline and commit/push changes to GitHub.


```sh
##new environment variables to be added to environment##
SONARSERVER = 'sonarserver'
SONARSCANNER = 'sonarscanner'
##new stages to be added##
 stage('CODE ANALYSIS with SONARQUBE') {
          
          environment {
             scannerHome = tool "${SONARSCANNER}"
          }
          steps {
            withSonarQubeEnv("${SONARSERVER}") {
               sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
            }
          }
   ``` 

- Our job will be triggerred and build automatically becauce of `the pull SM` cron job


- We can see quality gate results in SonarQube server.

- Let's create a Webhook in SonarQube to send the analysis results to jenkins

```sh
http://<private_ip_of_jenkins>:8080/sonarqube-webhook
   ``` 
- We will add below stage to our pipeline and commit changes to Github.


```sh
stage('QUALITY GATE') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
               waitForQualityGate abortPipeline: true
            }
            }
}
   ``` 
   
- Build the project 


<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

### :hammer_and_wrench: Artifact upload job
- We will automate publish latest artifact to Nexus repository after successful build. We also need to add `Build-Timestamp` to artifact name to get unique artifact each time we build. Go to `Manage Jenkins` -> `Configure System` under `Build Timestamp` add `yy-MM-dd_HHmm`.


- Also Add below stage to our pipeline

```sh
stage('UPLOAD ARTIFACT') {
                steps {
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                        groupId: 'QA',
                        version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                        repository: "${RELEASE_REPO}",
                        credentialsId: ${NEXUS_LOGIN},
                        artifacts: [
                            [artifactId: 'vproapp' ,
                            classifier: '',
                            file: 'target/vprofile-v2.war',
                            type: 'war']
                        ]
                    )
                }
        }
   ``` 
- Build the project 

- Artifact is uploaded to Nexus repository


<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

### :hammer_and_wrench: Connect all jobs together with-Build Pipeline

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

### :hammer_and_wrench: Set Automatic build trigger

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

### :hammer_and_wrench: Make Code change

- Add the code below to your Jenkinsfile in the same level with stages and push our changes.


```sh
post{
        always {
            echo 'Slack Notifications'
            slackSend channel: '#jenkinscicd',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
   ``` 
   
   
   

- We get our Notification from slack.

  
<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

## :earth_africa: Verify from browser

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>


## :page_facing_up: Resources

<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>


## :star2: Acknowledgment


<br/>
<div align="right">
    <b><a href="#Project-03">↥ back to top</a></b>
</div>
<br/>

