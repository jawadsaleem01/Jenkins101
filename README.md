# Table of Contents:
1. [installing jenkins in VM virtual Box ubuntu 18](#1-installing-jenkins-in-vm-virtual-box-ubuntu-18)
   1. Download ubuntu image
   2. Setup VM in virtual box
   3. install Docker Engine
   4. Install Docker Compose
   5. Install Docker Jenkins Image
   6. Create Docker Compose for Jenkins
   7. Create a Docker Container for Jenkins
   8. Create a Local DNS for Jenkins
 2. [Running first Job](#2-running-first-job)
 3. [Running first script from Job](#3-running-first-script)
 4. [Parameterize Job](#4-parameterize-job)
 5. [List Parameter Job](#5-list-parameter-job)
 6. [Boolean Parameter Job](#6-boolean-parameter-job)
 7. [Jenkisn On Docker Container](#7-jenkins-on-dockers)
 8. [Install SSH Plugin](#8-install-plugin-ssh)
 9. [Create a jenkins job using SSH](#9-create-a-jenkins-job-using-ssh)
 10. [Running MYSQL in docker](#10-running-mysql-in-docker)
 11. [install AWS CLI and mysql Client on docker](#11-install-aws-cli-and-mysql-client-on-docker)
 12. [Create a MySQL database](#12-create-a-mysql-database)
      12.1. [Create a table]
      12.2. [insert data in table]
 13. [Create a AWS S3 bucket](#13-take-mysql-backup-and-copy-in-aws-s3-bucket)
 14. [Create a Script to take backup]()
 15. [Integrate your script with AWS CLI]
 16. [Jenkins Add AWS SECRET and MYSQL Password in Credentials]
 17. Create Jenkins job to run the script
 18. Persist your script on remote-host 
 19. Install Ansible on jenkins 
 20. Create your first ansible inventory
 21. Create Ansible first playbook
 22. integrate Jenkins with Ansible (plugin)
 23. Execute Ansible Playbook fom jenkins job
 24. Pass paramters to ansible and jenkins
 25. Colorize your playbook output in Jenkins: AnsiColor Plugin
 26. Real Life Example JENKINS, ANSIBLE, NGINX, PHP, MYSQL and SHELL SCRIPTING
   ***********
# Jenkins101
    * [Install Jenkins Master Server](https://github.com/jawad1989/devops/tree/master/Jenkins)
    * Pipelines and Jobs
      * Creating your first Pipeline - with apache Maven
      * Creating Pipeline as Code - Jenkinsfile on Git
      * Free Style Job: Running Docker Containers
      * Free Style Job Running Java Code
      * How to Trigger a remote build in Jenkins
      * Create Job Chaining in Jenkins
      * Multi Branch Pipeline:
      * How to Create Parameterized Jenkins Job
      * Automated deployment pipeline
        * Deploying War file in Tomcat Server
      * Create and Configure Jenkins Slave Nodes using SSH 
      * Jenkins Creating/managing Users
    * Jenkings Creating API Token for GIT
    * Install Plugins in Jenkins 
      * GITLAB
      * Deploy to container
      * Blue ocean
      * Pipeline Build View
    * Updating Jenkins to Newer Version

# 1. installing jenkins in VM virtual Box ubuntu 18
 ### 1. Download ubuntu image
 ### 2. Setup VM in virtual box
 ### 3. install Docker Engine <br/>
   https://docs.docker.com/engine/install/ubuntu/
   * If you would like to use Docker as a non-root user, you should now consider adding your user to the “docker” group with something like:
```
  sudo usermod -aG docker your-user
```
 ### 4. Install Docker Compose <br/>
 https://docs.docker.com/compose/install/
 
 ### 5. Install Docker Jenkins Image
 https://hub.docker.com/_/jenkins/
 
 to get docker root directory
 ```
 docker info | grep -i root
 ```
 
 too see how much space docker is taking
 ```
 sudo du -sh /var/lib/docker
 ```
 ### 6. Create Docker Compose for Jenkins
 
   * create a new folder jenkins
   * cd in folder and mkdir jenkins_home for volume
   * create a new file 'vi docker-compose.yaml'
 ```
version: '3'

services:

  jenkins:

    container_name: jenkins

    image: jenkins

    ports:

      - "8080:8080"

    volumes:

      - $PWD/jenkins_home:/var/jenkins_home # left host vol : right docker vol

    networks:

      - net

networks:

  net: 
 ```
 
 
  docker compose is like a defination for what things we want

 ### 7. Create a Docker Container for Jenkins
  * make sure your user has proper rights on folders
  ```
  sudo chown 1000:1000 jenkins_home -R
  ```
  
  * run the docker compose
  ```
  docker-compose up -d
  ```
  
  verify by `docker ps` you will see the container running
  ![1](https://github.com/jawad1989/Jenkins101/blob/master/images/1.PNG)
  
  * run `docker logs -f jenkins` you will see the token which we will use later
  
  ![2](https://github.com/jawad1989/Jenkins101/blob/master/images/2%20-%20docker%20logs.PNG) 
  
  * get the ip from `ip a` and in your browser paste the ip:8080
  
  ![3](https://github.com/jawad1989/Jenkins101/blob/master/images/3%20-%20jenkins.PNG)
 
 * copy the code from `docker logs -f jenkins` and paste in jenkins and select either option as per need
 ![suggested](https://github.com/jawad1989/Jenkins101/blob/master/images/4%20-%20jenkins%20started.PNG)
 
 * plugins installing
 ![plugins](https://github.com/jawad1989/Jenkins101/blob/master/images/6%20-%20plugins%20installed.PNG)
 
 ![Jenkins](https://github.com/jawad1989/Jenkins101/blob/master/images/7%20-%20admin%20users.PNG)
 
 ![Jenkins](https://github.com/jawad1989/Jenkins101/blob/master/images/8%20-%20jenkins.PNG)
 
 ![Jenkins](https://github.com/jawad1989/Jenkins101/blob/master/images/9-jenkins.PNG)
 
 ![jenkins installed](https://github.com/jawad1989/Jenkins101/blob/master/images/10%20-jenknis.PNG)
 
 ### 8. Create a Local DNS for Jenkins
 
 * Open notepad with admin rights
 * goto `C:\Windows\System32\drivers\etc`
 * enter your ip in our case its `192.168.56.104 jenkins.local`
 * goto brower and type `jenkins.local:8080` this will bring up the jenkins

![jenkins dns](https://github.com/jawad1989/Jenkins101/blob/master/images/11%20-%20local%20dns.PNG)


> to stop docker compose service docker-compose stop
> to start docker-compose start
> docker-compose restart
> docker-compose down this will delete everything
> docker-compose up -d create docker containers
> docker exec -ti jenkins bash = this will login container

# 2. Running first Job 

In this example we will create a java helloworld file on voludme(that will automatically) move to docker mount point.

```
cd /home/jawad/jenkins_home/
mkdir javaExample
touch HelloWorld.java
gedit HelloWorld.java
```

copy below code in HelloWorld.java
```
class HelloWorld 
{ 
    // Your program begins with a call to main(). 
    // Prints "Hello, World" to the terminal window. 
    public static void main(String args[]) 
    { 
        System.out.println("Hello, World"); 
    } 
} 
```

goto your jenkins dashaord create a new job, in shell run the java file

```
pwd
cd /var/jenkins_home/javaExample
javac HelloWorld.java
java HelloWorld
```
![java](https://github.com/jawad1989/Jenkins101/blob/master/images/1%20-%20jenkins%20java.PNG)


Build the jenkins job

goto console you will see the output

```
Started by user jawad
Running as SYSTEM
Building in workspace /var/jenkins_home/workspace/MyFirstJob-May27-540PM
[MyFirstJob-May27-540PM] $ /bin/sh -xe /tmp/jenkins6324372263307166450.sh
+ pwd
/var/jenkins_home/workspace/MyFirstJob-May27-540PM
+ cd /var/jenkins_home/javaExample
+ javac HelloWorld.java
+ java HelloWorld
Hello, World
Finished: SUCCESS
```

# 3. Running first script

in this job we will create a simple script that will take name and last name as parameters and return Hello name and lastname

* cd into jekins home
* mkdir jekins_data
* gedit script.sh and put below code
```
#!/bin/bash

NAME=$1
LAST=$2

echo "Hello, $NAME, $LAST"
```

* goto jenkins create a job, goto configure and in Build section copy below commads

```
cd /var/jenkins_home/jekins_data/
./script.sh JAWAD SALEEM
```
* build the job and see the output
![output](https://github.com/jawad1989/Jenkins101/blob/master/images/2-%20console%20output.PNG)


# 4. Parameterize Job

We will use the script in the last example, but this time we will take the first and last name from user 
* create a new job
* in General section click `This project is parameterized`
   ![Adding PArametes](https://github.com/jawad1989/Jenkins101/blob/master/images/3%20-Adding%20Parametes.PNG)
* in Build us the parameters
```
cd /var/jenkins_home/jekins_data/
./script.sh $FIRST_NAME $LAST_NAME
```

* build the job, jenkins will ask for the values and show the default values

![sneding params](https://github.com/jawad1989/Jenkins101/blob/master/images/4%20-%20giving%20parameters.PNG)

* see the CONSOLE output 
```
Started by user jawad
Running as SYSTEM
Building in workspace /var/jenkins_home/workspace/ParameterizedJob-May27-635PM
[ParameterizedJob-May27-635PM] $ /bin/sh -xe /tmp/jenkins235872662254780424.sh
+ cd /var/jenkins_home/jekins_data/
+ ./script.sh HANIYA JAWAD
Hello, HANIYA, JAWAD
Finished: SUCCESS
```

# 5. List Parameter Job

We can use list for the parameter, to use this use `choice parameter` in jenkins

![Choice](https://github.com/jawad1989/Jenkins101/blob/master/images/choice%20param.PNG)

![Choice b](https://github.com/jawad1989/Jenkins101/blob/master/images/choice%20param%20b.PNG)

# 6. Boolean Parameter Job

Select Boolean option in jenkins job, if Default is checked it means true

![Boolean](https://github.com/jawad1989/Jenkins101/blob/master/images/boolean.PNG)


Script to be used

```
#!/bin/bash

NAME=$1
LAST=$2
SHOW=$3

if [ "$SHOW" = "true" ]; then
 echo "Hello, $NAME $LAST"
else
 echo "Kindly mark SHOW option to show names"
fi

```
# 7. Jenkins on Dockers

## 7.1. Docker + Jenkins + SSH

* Create a new directory `centos`
* pwd should return `/home/jawad/jenkins/jenkins_home/jekins_data/centos`
* Create a Dockerfile
we are installing a centos image, instlaling openssh server, creating a user, setting password, creating a new home directory and giving write permissions to the new user
```
FROM centos

RUN yum -y install openssh-server
RUN useradd remote_user && \
    echo "remote_user:1234" | chpasswd && \
    mkdir /home/remote_user/.ssh && \
    chmod 700 /home/remote_user/.ssh
```

* Create a SSH key
 1. type `ssh-keygen -f remote-key` and press enter 3 times
 ```
 Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in remote-key.
Your public key has been saved in remote-key.pub.
The key fingerprint is:
SHA256:nrXFrSiX6H1nKUkPaY5CVVC966vL3XzJLknczbqIK+A jawad@jawad-VirtualBox
The key's randomart image is:
+---[RSA 2048]----+
|          .o..   |
|            . .  |
|           .   . |
|          .. ..  |
|        S.. +.oo.|
|      ...+ ==+..o|
|     . o= ==o=oo.|
|      E.o+.o==B=.|
|        .o+o=**++|
+----[SHA256]-----+

 ```
2. do a ls you will notice two files created
```
ll
total 20
drwxr-xr-x 2 jawad jawad 4096 May 28 10:56 ./
drwxr-xr-x 3 jawad jawad 4096 May 28 10:40 ../
-rw-r--r-- 1 jawad jawad  196 May 28 10:47 Dockerfile
-rw------- 1 jawad jawad 1679 May 28 10:56 remote-key
-rw-r--r-- 1 jawad jawad  404 May 28 10:56 remote-key.pub

```

3. remote_key is a certificate key/private key
4. remote_key.pub is a public key
5. add more commands in Dockerfile
jawad@jawad-VirtualBox:~/jenkins/centos$ cat Dockerfile 
```
FROM centos

RUN yum -y install openssh-server

RUN useradd remote_user && \
    echo "remote_user:1234" | chpasswd && \
    mkdir /home/remote_user/.ssh && \
    chmod 700 /home/remote_user/.ssh

COPY remote-key.pub /home/remote_user/.ssh/authorized_keys

RUN chown remote_user:remote_user -R /home/remote_user/.ssh/ &&\
    chmod 600 /home/remote_user/.ssh/authorized_keys

#RUN /usr/sbin/sshd-keygen
RUN ssh-keygen -A && rm -rf /run/nologin

CMD /usr/sbin/sshd -D
```
6. update docker-compose file
  here we will add our new image "remote_host"
  ```
  version: '3'
services:
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins
    ports:
      - "8080:8080"
    volumes:
      - $PWD/jenkins_home:/var/jenkins_home # left host vol : right docker vol
    networks:
      - net
  remote_host:
    container_name: remote-host
    image: remote-host
    build:
      context: centos
    networks:
      - net
networks:
  net:
  ```
 7. build new docker image 'remote_user' by running `docker-compose build`
 8. now if you run 'docker images' you will see the new image 'remote_user' but still container is not created
 9. run 'docker-compose up -d' to run the container. in case of any error run 'dokcer logs container_id'
 10. run 'docker ps' to see the container
 11. now test your new ssh connection by going into jenkins
     ```
     docker exec -it jenkins bash
     
     ssh remote_user@remote_host
     give password 1234 you will be logged in
     ```
 12. log in via 'remote-key' which we generated earlier
 13. exit and goto /centos directory and run below command
 ```
 docker cp remote-key jenkins:/tmp/remote-key
 ```
 14. goto jenkins 'docker exec -it jenkins bash' and `cd /tmd` you will see the remote-key file here
 15. login using remote-key
 ```
 ssh -it remote-key remote_user@remote_host
 ```
 you will be logged in without giving password


# 8. Install Plugin: ssh
  1. goto jenkins dashboad
  2. goto manage jenkins
  3. goto manage plugins
  4. goto available, type `ssh`
  5. click ssh and click install with out restart
  
   ![ssh](https://github.com/jawad1989/Jenkins101/blob/master/images/1-plugin-ssh.PNG)
   
  6. click restart option and wait for jenkins to restart
  
   ![restart](https://github.com/jawad1989/Jenkins101/blob/master/images/2-restart%20shh.PNG)
   
  7. after restart, goto credentials, add a new global credential
  
  ![add credentials](https://github.com/jawad1989/Jenkins101/blob/master/images/2b-credentials.PNG)
  
  we also have to copy the private `remote_key` for that we can goto jenkins container and copy that.
  
  ![add new credentials](https://github.com/jawad1989/Jenkins101/blob/master/images/2c-add-credentials.PNG)
  
  8. goto `Manage jenkins->Configure System` in ssh add a new connection with below details and test it
  
  ![manage ssh](https://github.com/jawad1989/Jenkins101/blob/master/images/3%20b%20-%20add%20ssh.PNG)
  
# 9. Create a jenkins job using SSH 
 1. Create a new freestyle job
 2. Select `Execute shell script on remote host using ssh`
 3. add the below command in build
 
 ```
 NAME=JAWAD
 echo "Hello, $NAME and current date is $(date)" > /tmp/remote-file-jenkins
 ```
 
 4. click save and click build job
 5. Once Successfull you can ssh into `remote_host` and cat the file on container 
 
 ![tmp](https://github.com/jawad1989/Jenkins101/blob/master/images/4-build-ssh.PNG)

# 10. Running MYSQL in docker

https://hub.docker.com/_/mysql

1. Create/update `docker-compole.yml`
  ***MYSQL_ROOT_PASSWORD*** is required by mysql image, you can check in mysql image link provided above. Also mysql volume location would be `/var/lib/mysql` also can be verified in above link.
  
 ```
version: '3'
services:
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins
    ports:
      - "8080:8080"
    volumes:
      - $PWD/jenkins_home:/var/jenkins_home # left host vol : right docker vol
    networks:
      - net
  remote_host:
    container_name: remote-host
    image: remote-host
    build:
      context: centos
    networks:
      - net
  db_host: 
    container_name: mysql
    image: mysql:5.7
    environment: 
      - "MYSQL_ROOT_PASSWORD=1234"
    volumes: 
      - "$PWD/db_data:/var/lib/mysql"
    networks:
      - net
networks:
  net: 

 ```
 2. Start docker-compose by running `docker-compose up -d`
 3. Run `docker ps` to verify
 4. Check if mysql is up and runnin by running `docker logs -f mysql`
 5. login into mysql 
 ```
 docker exec -ti mysql bash
 ```
 6. connect mysql using user `root` and password `1234` as given in docker-compose file and run `show databases`
 ```
 mysql -u root -p
 show databases;
 ```
 ![show databases](https://github.com/jawad1989/Jenkins101/blob/master/images/5-show%20databases.PNG)
 
 # 11. install AWS CLI and mysql Client on docker
  1. update `Dockerfile` in `centos` directory for `remote_host`
  
  ```
  FROM centos

RUN yum -y install openssh-server

RUN useradd remote_user && \
    echo "remote_user:1234" | chpasswd && \
    mkdir /home/remote_user/.ssh && \
    chmod 700 /home/remote_user/.ssh

COPY remote-key.pub /home/remote_user/.ssh/authorized_keys

RUN chown remote_user:remote_user -R /home/remote_user/.ssh/ &&\
    chmod 600 /home/remote_user/.ssh/authorized_keys

#RUN /usr/sbin/sshd-keygen
RUN ssh-keygen -A && rm -rf /run/nologin

#intall mysql
RUN yum -y install mysql

# install AWS CLI
RUN yum -y install epel-release && \
    yum -y install python3-pip && \
    pip3 install --upgrade pip && \
    pip3 install awscli

CMD /usr/sbin/sshd -D

  ```
  
  2. build `docker-compose build`  and run the container `docker-compose up -d`
  3. exec into remote_hose and verify aws/mysal
  ```
  docker exec -ti remote_host bash
  aws
  mysql
  ```
# 12. Create a MYSQL Database
 1. Connect to `remote_host` comtainer
 ```
 docker exec -ti remote-host bash
 ```
 2. connect mysql
 ```
 mysql -u root -h db_host -p
 ```
 3. Once logged in, run below commands 
 ```
 SHOW DATABASES;
 CREATE DATABASE testdb;
 use testdb;
 create table info (name varchar(20), lastname varchar(20), age int(2));
 show tables;
 insert into info values ('jawad', 'saleem', 30);
 desc info;
 select * from info;
 ```
 
 # 13. Take MYsql backup and copy in AWS S3 bucket
 Pre Req:
 Create a IAM user on AWS and assign S3 Full Access
 
 1. login in `remote_host`
 ```
 docker exec -ti remote-host bash
 ```
 2. take backup and place in tmp folder
 ```
 mysqldump -u root -h db_host -p testdb /tmp/dbbkp.sql
 ```
 3. you can verify by doing a cd in tmp and cat back up file
 4. now move the file to AWS S3 bucket
 ```
 aws s3 cp dbbkp.sql s3://jenkins-mysql-backup-jawad/dbbkp.sql
 output: upload: ./dbbkp.sql to s3://jenkins-mysql-backup-jawad/dbbkp.sql
 ```
 ![aws s3](https://github.com/jawad1989/Jenkins101/blob/master/images/6-%20aws%20s3%20bkp%20sql.PNG)
 
 


AKIAZPZIIH2GW5BNQ6GB
tnK6rvBRjiWEl12pDXfMnSB/lF4nAlLZZhaauZSj


# 14. Create a Script to take backup
1. follow below commnads to create a script in temp directory that will genearte a backup fil in tmp directory
```
cd
vi /tmp/script.sh
```

in script.sh
```
#/bin/bash

DATE=$(date +%H-%M-%S)
DB_HOST=$1
DB_PASSWORD=$2
DB_NAME=$3

mysqldump -u root -h $DB_HOST -p$DB_PASSWORD $DB_NAME > /tmp/db-$DATE.sql
```

2. give write/execute permisions 
```
chmod +x /tmp/script.sh
```
3. run script
```
/tmp/script.sh
```
4. verify backup is created in tmp directory
```
cd /tmp/
ls
```

# 15. Integrate your script with AWS CLI

open the script in vi `vi /tmp/script.sh`
```
#/bin/bash

DATE=$(date +%H-%M-%S)
$BACUP_FILE=db_$DATE.sql

DB_HOST=$1
DB_PASSWORD=$2
DB_NAME=$3
$AWS_SECRET=$4
$BUCKET_NAME=$5

mysqldump -u root -h $DB_HOST -p$DB_PASSWORD $DB_NAME > /tmp/$BACKUP_FILE

export AWS_ACCESS_KEY_ID=AKIAZPZIIH2GW5BNQ6GB && \
export AWS_SECRET_ACCESS_KEY=$AWS_SECRET && \
echo "uploading mysql backup to to aws s3 bucket" && \
aws s3 cp /tmp/$BACKUP_FILE s3://$BUCKET_NAME/$BACKUP_FILE
```

# 16. [Jenkins Add AWS SECRET and MYSQL Password in Credentials

1. Goto jenkins dasboard
2. goto credentials. press add new
3. select 'SECRET with text' and create one for `MYSQL_PASSWORD` and other for `AWS_SECRET_KEY`

![MYSQL Password](https://github.com/jawad1989/Jenkins101/blob/master/images/7-JENKINS-MYSQL-credential.PNG)

![AWS](https://github.com/jawad1989/Jenkins101/blob/master/images/7-JENKINS-AWS-SECRET-KEY-credential.PNG)


# 17. Create Jenkins job to run the script

* update the script.sh to accept more parameters and upload file to AWS

```
#/bin/bash

DATE=$(date +%H-%M-%S)
BACKUP_FILE=db_$DATE.sql

DB_HOST=$1
DB_PASSWORD=$2
DB_NAME=$3
AWS_SECRET=$4
BUCKET_NAME=$5

mysqldump -u root -h $DB_HOST -p$DB_PASSWORD $DB_NAME > /tmp/$BACKUP_FILE

export AWS_ACCESS_KEY_ID=AKIAZPZIIH2GW5BNQ6GB && \
export AWS_SECRET_ACCESS_KEY=$AWS_SECRET && \
echo "uploading mysql backup to to aws s3 bucket" && \
aws s3 cp /tmp/$BACKUP_FILE s3://$BUCKET_NAME/$BACKUP_FILE

```
* In jenkins create a free style job and three parameters 

1. String Parameter
NAME:MYSQL_HOST
DEFAULT:db_host

2. STRING PARAMETER
NAME: DATABASE_NAME
VALUE: testdb

3. STRING PARAMETER
NAME: AWS_S3_BUCKET_NAME
DEFAULT: jenkins-mysql-backup-jawad

* In build  Environment section check `Use secret text(s) or file(s)` and add those two secret file parameters
![bindings](https://github.com/jawad1989/Jenkins101/blob/master/images/8-bindinds.PNG)

* Run the job and job will be successfull
Console Output:
```
Started by user jawad
Running as SYSTEM
Building in workspace /var/jenkins_home/workspace/AWS-S3-SQL-BACKUP-MAY31st-1117PM
[SSH] script:
AWS_S3_BUCKET_NAME="jenkins-mysql-backup-jawad"
MYSQL_HOST="db_host"
AWS_SECRET_KEY=**********
MYSQL_PASSWORD=**********
DATABASE_NAME="testdb"

/tmp/script.sh $MYSQL_HOST $MYSQL_PASSWORD $DATABASE_NAME $AWS_SECRET_KEY $AWS_S3_BUCKET_NAME

[SSH] executing...
mysqldump: [Warning] Using a password on the command line interface can be insecure.
mysqldump: Couldn't execute 'SELECT COLUMN_NAME,                       JSON_EXTRACT(HISTOGRAM, '$."number-of-buckets-specified"')                FROM information_schema.COLUMN_STATISTICS                WHERE SCHEMA_NAME = 'testdb' AND TABLE_NAME = 'info';': Unknown table 'COLUMN_STATISTICS' in information_schema (1109)
uploading mysql backup to to aws s3 bucket
Completed 1.4 KiB/1.4 KiB (4.2 KiB/s) with 1 file(s) remaining
upload: ../../tmp/db_05-01-19.sql to s3://jenkins-mysql-backup-jawad/db_05-01-19.sql

[SSH] completed
[SSH] exit-status: 0

Finished: SUCCESS
```


18, Persist your script on remote-host

if we remove the container remote-host our script will be deleted as we are not using volumes, to sovle this update the docker-compose file as per below and also copy/create script.sh to jenkins folder as `aws-s3.sh` so we can use this to copy into remote-host tmp folder
```
version: '3'
services:
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins
    ports:
      - "8080:8080"
    volumes:
      - $PWD/jenkins_home:/var/jenkins_home # left host vol : right docker vol
    networks:
      - net
  remote_host:
    container_name: remote-host
    image: remote-host
    build:
      context: centos
    volumes:
      - "$PWD/aws-s3.sh:/tmp/script.sh"
    networks:
      - net
  db_host: 
    container_name: mysql
    image: mysql:5.7
    environment: 
      - "MYSQL_ROOT_PASSWORD=1234"
    volumes: 
      - "$PWD/db_data:/var/lib/mysql"
    networks:
      - net
networks:
  net:
```
now test it by removing the conainer
```
docker rm -fv remote-host
```

recreate it 
```
docker-compose up -d
```


# 19. Install Ansible on jenkins 

1. in your jenkins directory create a new folder `jenkins-ansible`
```
mkdir jenkins-ansible
```
2. cd into jenkins-ansible directory and create a `Dockerfile` and below is the content of that file
```
FROM jenkins/jenkins

USER root

RUN apt-get update && apt-get install python3-pip -y && \
    pip3 install ansible --upgrade
    
USER jenkins
```

3. cd back into jenkins folder and update the `docker-compose.yml`
```
version: '3'
services:
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins
    build:
      context: jenkins-ansible  #docker file path 
    ports:
      - "8080:8080"
    volumes:
      - $PWD/jenkins_home:/var/jenkins_home # left host vol : right docker vol
    networks:
      - net
  remote_host:
    container_name: remote-host
    image: remote-host
    build:
      context: centos
    volumes:
      - "$PWD/aws-s3.sh:/tmp/script.sh"
    networks:
      - net
  db_host: 
    container_name: mysql
    image: mysql:5.7
    environment: 
      - "MYSQL_ROOT_PASSWORD=1234"
    volumes: 
      - "$PWD/db_data:/var/lib/mysql"
    networks:
      - net
networks:
  net:
```

4. build docker compose and start the container
```
docker-compose build
docker-compose up -d
```

5. create a new directory `ansible` in jenkins_home (volume for jenkins)  and copy the `centos/remote-key` that directory 

```
mkdir jenkins_home/ansible
cp centos/remote-key jenkins_home/ansible
```

6. verify if file is created/copied login into jenkins container
```
docker exec -ti jenkins bash
cd $HOME
pwd
cd ansible
ls 
# you will see the remote-key here
```

# 20. Create your first ansible inventory
[OFFICIAL ANSIBLE DESCRIPTION](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html) Ansible works against multiple managed nodes or “hosts” in your infrastructure at the same time, using a list or group of lists know as inventory. Once your inventory is defined, you use patterns to select the hosts or groups you want Ansible to run against.

The default location for inventory is a file called /etc/ansible/hosts. You can specify a different inventory file at the command line using the -i <path> option
   
1. first step is to going into jenkins-ansible directory and copying centos/remote-key here
```
cd ..jenkins/jenkins-ansible
cp ../centos/remote-key .
```
2. now create a hosts file  `vi hosts`
```
[all:vars] 

ansible_connection = ssh

[test]   
test1 ansible_host=remote_host ansible_user=remote_user ansible_private_key_file=/var/jenkins_home/ansible/remote-key  
```
3. copy the hosts to jenkins ansible volume
```
cp hosts ../jenkins_home/ansible
```
4. move into jenkins container and ping the remote-host from ansible where test1 is our alias
```
docker exec -ti jenkins bash
ansible -i hosts -m ping test1

```
5. you will get below success message
```
test1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false,
    "ping": "pong"
}
```


# 21. Create Ansible first playbook

playbooks are the basis for a really simple configuration management and multi-machine deployment system, unlike any that already exist, and one that is very well suited to deploying complex applications.

1. goto jenkins-ansible directory and create a play.yml file `gedit play.yml`
```
- hosts: test1
  tasks: 
   - shell: echo Hello World > /tmp/ansible-file
```

2. copy the play.yml in volume of ansible
```
cp play.yml ../jenkins_home/ansible/
```
3. run the playbook
```
docker exec -ti jenkins bash
cd $HOME/ansbile 
ls 
ansible-playbook -i hosts play.yml
```
output
```
jenkins@92300ec66809:~/ansible$ ansible-playbook -i hosts play.yml 

PLAY [test1] ***********************************************************************************

TASK [Gathering Facts] *************************************************************************
ok: [test1]

TASK [shell] ***********************************************************************************
changed: [test1]

PLAY RECAP *************************************************************************************
test1                      : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

4. goto remote host to verify the file in tmp directory
```
docker exec -ti remote-host bash
cd /tmp/
ls
cat ansible-file
```

output:
```
[root@257da7ee86a1 tmp]# cat ansible-file 
Hello World
```
#  22. integrate Jenkins with Ansible

1. Login to your jenkins via browser and goto to `Manage jenkins`
2. click `manage plugins` and goto available
3. Search ansible and install without restart
![installl ansible](https://github.com/jawad1989/Jenkins101/blob/master/images/9-install%20ansible%20plugin.PNG)
4. once installed click restart and wait for restart
![install ansible](https://github.com/jawad1989/Jenkins101/blob/master/images/9-b%20-install%20ansible%20plugin.PNG)


# 23. Execute Ansible Playbook fom jenkins job

1. Create a new `Free Style Job`
2. Goto Build and select `nvoke Ansible Playbook` in playbook path `/var/jenkins_home/ansible/play.yml` and select Intventory->File or Host with value `/var/jenkins_home/ansible/hosts`
![ansible ](https://github.com/jawad1989/Jenkins101/blob/master/images/10-ansible%20play%20jenkins.PNG)

3. Build the job,once successful
![console](https://github.com/jawad1989/Jenkins101/blob/master/images/11%20-%20console%20output.PNG)

4. goto remote host to verify the file
```
Jawad@jawad-VirtualBox:~/jenkins$ docker exec -ti remote-host bash
[root@257da7ee86a1 /]# cat /tmp/ansible-file 
Hello Jawad

```
# 24. Pass paramters to ansible and jenkins
1. Now in this example we will modify our play book and instead of debug we will use debug so create a new file `jawad@jawad-VirtualBox:~/jenkins$ gedit jenkins_home/ansible/play.yml `

```
- hosts: test1
  tasks:
    debug:
      msg: "{{ MSG }}"
```

2. Goto your jenkins job and add a string parameter 
```
NAME: ANSINLE_MSG
value: jawad saleem
```
![paramter](https://github.com/jawad1989/Jenkins101/blob/master/images/12%20-%20a%20paramters.PNG)

3. in build, click advance, and add new EXTRA VARIABLES give the values `KEY: MSG VALUE: ANSIBLE_MSG`
![paramter](https://github.com/jawad1989/Jenkins101/blob/master/images/12-%20variables.PNG)

4. build job and enter the paramter
![paramter](https://github.com/jawad1989/Jenkins101/blob/master/images/13%20-%20build.PNG)

5. Console output
![console](https://github.com/jawad1989/Jenkins101/blob/master/images/14%20-%20console.PNG)

# 25. Colorize your playbook output in Jenkins: AnsiColor Plugin

1. In Jenking Plugin Manager add a new plugin `AnsiColor` and install without restart
![ansi color](https://github.com/jawad1989/Jenkins101/blob/master/images/15%20-%20ansi%20color.PNG)

2. goto your jenkins job 
  * in build check "Color ANSI Console Output"
  * in advanced check "Colorized stdout" and save
3. Build the job
![Colored Console](https://github.com/jawad1989/Jenkins101/blob/master/images/16%20-%20Colored%20Output.PNG)

# 26. Real Life Example JENKINS, ANSIBLE, NGINX, PHP, MYSQL and SHELL SCRIPTING
 ## Create a new database and table in mysql
 
 prereq: create a new database `people` and new table `register`
 ```
 docker exec -ti mysql bash
 mysql -u root -p
 1234
 create database people;
 use people;
 create table register (id int(3), name varchar(50), lastname varchar(50), age int(3));
 ```
 1. goto mysql container, create a new database and a table
  ```
  docker exec -ti mysql bash
  create database people;
  use people;
  create table register ( id int(3), name varchar(50), lastname varchar(50), age int(3));
  show tables;
  desc register;
  ```
  2. Create a bash script to feed your Database
  * create a file `people.txt` with some random data of user like
  ```
Denice,Caudle  
Cherise,Olenick  
Nohemi,Overlock  
Tom,Fellers  
Teri,Mess  
Violette,Zawislak  
Gisele,Cremeans  
Rey,Sturdevant  
Jeannine,Hysell  
Preston,Manigault  
Cinda,Dales  
Shaniqua,Barnaby  
Julieann,Fountaine  
Georgianna,Leigh  
Jana,Putnam  
Kimber,Doescher  
Eli,Moya  
Louanne,Osbourn  
Elaine,Sindelar  
Keren,Beecham  
Pamella,Nordstrom  
Venice,Rudisill  
Monroe,Sires  
Rosalinda,Strickler  
Juli,Laclair  
Violeta,Rosier  
Janessa,Bearden  
Dani,Tokarz  
Danial,Nesler  
Love,Ashford  
Horace,Jerez  
Lanita,Rizzi  
Wilburn,Human  
Ike,Bolyard  
Victorina,Bethea  
Tamisha,Sweetman  
Tyrell,Giorgi  
Andra,Redmon  
Kristina,Collins  
Adah,Sidebottom  
Fannie,Spector  
Kittie,Jacobus  
Marcy,Bronstein  
Lavon,Degraffenreid  
Mabel,Ecker  
Branden,Tippie  
Wendell,Saner  
Meg,Feely  
Lavone,Saam  
Rachele,Lichtenberger 
  
  ```
  * create a script `put.sh` in jenkins/jenkins-ansible directory
  ```
  #!/bin/bash

counter=0

while [ $counter -lt 50 ]; do
  let counter=counter+1

  name=$(nl people.txt | grep -w $counter | awk '{print $2}' | awk -F  ',' '{print $1}')
  lastname=$(nl people.txt | grep -w $counter | awk '{print $2}' | awk -F  ',' '{print $2}')
  age=$(shuf -i 20-40 -n 1)
  #echo "firstname = $name , lastname= $lastname"

  mysql -u root -p1234 people -e "insert into register values($counter,'$name','$lastname',$age)"
  echo "$counter, $name, $lastname, $age insserted successfully"
done
  ```
  
  3. give execute permission to script
  ```
  chmod +x put.sh
  ```
  
  4. copy the script and people.txt to `mysql` containter 
  ***docker cp people.txt mysql:/tmp***
  ***docker cp put.sh mysql:/tmp***
  
  5. exec into `mysql` container
  ```
  docker exec -ti mysql bash
  ```
  
  6.  goto tmp folder and run the script
  ```
  cd /tmp/
  ./put.sh
  ```
  
  output:
  ```
  ./put.sh
mysql: [Warning] Using a password on the command line interface can be insecure.
1, Denice, Caudle, 32 insserted successfully
mysql: [Warning] Using a password on the command line interface can be insecure.
2, Cherise, Olenick, 28 insserted successfully
mysql: [Warning] Using a password on the command line interface can be insecure.
3, Nohemi, Overlock, 34 insserted successfully
mysql: [Warning] Using a password on the command line interface can be insecure.
4, Tom, Fellers, 22 insserted successfully
mysql: [Warning] Using a password on the command line interface can be insecure.
5, Teri, Mess, 27 insserted successfully
mysql: [Warning] Using a password on the command line interface can be insecure.
6, Violette, Zawislak, 20 insserted successfully
mysql: [Warning] Using a password on the command line interface can be insecure.
7, Gisele, Cremeans, 20 insserted successfully
mysql: [Warning] Using a password on the command line interface can be insecure.
8, Rey, Sturdevant, 34 insserted successfully
mysql: [Warning] Using a password on the command line interface can be insecure.
9, Jeannine, Hysell, 30 insserted successfully
mysql: [Warning] Using a password on the command line interface can be insecure.
10, Preston, Manigault, 39 insserted successfully
mysql: [Warning] Using a password on the command line interface can be insecure.
11, Cinda, Dales, 21 insserted successfully
mysql: [Warning] Using a password on the command line interface can be insecure.
12, Shaniqua, Barnaby, 33 insserted successfully
mysql: [Warning] Using a password on the command line interface can be insecure.
13, Julieann, Fountaine, 39 insserted successfully
mysql: [Warning] Using a password on the command line interface can be insecure.


  ```
  
  7.  goto mysql db and verify if data is inserted

```
  mysql -u root -p 
  1234
  use people;
  select * from register;
 ```
 
 ![register](https://github.com/jawad1989/Jenkins101/blob/master/images/17%20-%20show%20register%20table.PNG)


 ## Creae a Docker Nginx web server + php
 
 1. for running ngin we have to use centos7 thats why Dockerfile in  centos will be updated
 2. clonse he web/ folder in jenkins-ansible
 3. In web directory there is a Dockerfile
 
 ```
 FROM remote-host

COPY ./conf/nginx.repo /etc/yum.repos.d/nginx.repo

RUN                                                                          \
  yum -y install nginx-1.12.2 openssl --enablerepo=nginx                  && \
  yum -y install https://repo.ius.io/ius-release-el7.rpm                  && \
  yum -y install                                                             \
    php71u-fpm                                                               \
    php71u-cli                                                               \
    php71u-mysqlnd                                                           \
    php71u-soap                                                              \
    php71u-xml                                                               \
    php71u-zip                                                               \
    php71u-json                                                              \
    php71u-mcrypt                                                            \
    php71u-mbstring                                                          \
    php71u-zip                                                               \
    php71u-gd                                                                \
     --enablerepo=ius-archive && yum clean all

EXPOSE 80 443

VOLUME /var/www/html /var/log/nginx /var/log/php-fpm /var/lib/php-fpm

COPY ./conf/nginx.conf /etc/nginx/conf.d/default.conf

COPY ./bin/start.sh /start.sh

RUN chmod +x /start.sh

CMD /start.sh
 ```
 this will use 'remote-host' Docker file as a pre-req to create the image, then we will configure nginx and install php. After that we will run the script
 
 3. goto your docker-compose.yml and add a new service `web`
 ```
 version: '3'
services:
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins
    build:
      context: jenkins-ansible  #docker file path 
    ports:
      - "8080:8080"
    volumes:
      - $PWD/jenkins_home:/var/jenkins_home # left host vol : right docker vol
    networks:
      - net
  remote_host:
    container_name: remote-host
    image: remote-host
    build:
      context: centos
    volumes:
      - "$PWD/aws-s3.sh:/tmp/script.sh"
    networks:
      - net
  db_host: 
    container_name: mysql
    image: mysql:5.7
    environment: 
      - "MYSQL_ROOT_PASSWORD=1234"
    volumes: 
      - "$PWD/db_data:/var/lib/mysql"
    networks:
      - net
  web:
    container_name: web
    image: ansible-web
    build:
      context: jenkins-ansible/web
    ports:
      - "80:80"
    networks:
      - net
networks:
  net: 

 ```

 ## Build a Table using HTML + PHP in container
 
 1. use table.js
 ```
 <!DOCTYPE html>
<html>
<head>
 <title>Table with database</title>
 <style>
  table {
   border-collapse: collapse;
   width: 100%;
   color: #588c7e;
   font-family: monospace;
   font-size: 25px;
   text-align: left;
     }
  th {
   background-color: #588c7e;
   color: white;
    }
  tr:nth-child(even) {background-color: #f2f2f2}
 </style>
</head>
<body>
 <table>
 <tr>
  <th>id</th>
  <th>name</th>
  <th>lastname</th>
  <th>age</th>
 </tr>
 <?php
  $conn = mysqli_connect("db", "root", "1234", "people");
  // Check connection
  if ($conn->connect_error) {
   die("Connection failed: " . $conn->connect_error);
  }
  $sql = "SELECT id, name, lastname, age FROM register {% if PEOPLE_AGE is defined %} where age = {{ PEOPLE_AGE }} {% endif %}";
  $result = $conn->query($sql);
  if ($result->num_rows > 0) {
   // output data of each row
   while($row = $result->fetch_assoc()) {
    echo "<tr><td>" . $row["id"]. "</td><td>" . $row["name"] . "</td><td>"
. $row["lastname"]. "</td><td>" . $row["age"]. "</td></tr>";
  }
    echo "</table>";
  } else { echo "0 results"; }
  $conn->close();
?>
</table>
</body>
</html>
 ```
 2. copy this table.js in index.php in web server
 ```
 docker cp table.j2 web:/var/www/html/index.php
 ```
 
 3. Now if we goto browser at port 80 we will see the table
 ![test html](https://github.com/jawad1989/Jenkins101/blob/master/images/18%20-%20display%20table.PNG)

 ## Integrate Docker Server to Ansible inventory
 For doing this you have to create a new connection in `hosts` file
 1. goto ansible volume in your local vm
 ```
 cd /home/jawad/jenkins/jenkins_home/ansible
 ```
 2. update hosts file `gedit hosts` and add below rows
 ```
 [all:vars]

ansible_connection = ssh

[test]

test1 ansible_host=remote_host ansible_user=remote_user ansible_private_key_file=/var/jenkins_home/ansible/remote-key
web1 ansible_host=web ansible_user=remote_user ansible_private_key_file=/var/jenkins_home/ansible/remote-key0
 ```
 
 3. create a new `.ansible.cfg` file `gedit .ansible.cfg` with below content
 ```
[ssh_connection]
ssh_args = -C -o ControlMaster=auto -o ControlPersist=60s

control_path = /dev/shm/cp%%h-%%p-%%r
 ```
 
 4. goto jenkins container `docker exec -ti jenkins bash`
 5. goto ansible directory  `cd $HOME/ansible`
 6. run  command `ssh-keygen -f "/var/jenkins_home/.ssh/known_hosts" -R remote_host`
 7. test your connections
 ```
 jenkins@d8b78529f63e:~/ansible$ ansible -m ping -i hosts web1
web1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}

 ```
 8. verify test1 connection also
 ```
 jenkins@d8b78529f63e:~/ansible$ ansible -m ping -i hosts test1
Warning: the ECDSA host key for 'remote_host' differs from the key for the IP address '172.18.0.4'
Offending key for IP in /var/jenkins_home/.ssh/known_hosts:2
Matching host key in /var/jenkins_home/.ssh/known_hosts:5
Are you sure you want to continue connecting (yes/no)? yes
test1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
 ```
 9. to test all connections use `ansible -m ping -i hosts all` command
 ```
 jenkins@d8b78529f63e:~/ansible$ ansible -m ping -i hosts all
test1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
web1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}

 ```

 ## Create a playbook to update web table
 in table.j2 we are using [jinja2 template for passing vairable](https://jinja.palletsprojects.com/en/2.11.x/)
 
 1. create a new file `people.yml` in ansible directory
 ```
 jawad@jawad-VirtualBox:~/jenkins/jenkins_home/ansible$ cat people.yml 
- hosts: web1
  tasks:
   - name: Transfer Template To Web Server
     template: 
       src: table.j2
       dest: /var/www/html/index.php
 ```
 ![people yml](https://github.com/jawad1989/Jenkins101/blob/master/images/19%20-%20peope%20yml.PNG)
 
 2. now goto jenkins container and run the ansible playbook
 ```
 docker exec -ti jenkins bash
 cd
 cd ansible/
jenkins@d8b78529f63e:~/ansible$ ansible-playbook -i hosts people.yml

PLAY [web1] ************************************************************************************

TASK [Gathering Facts] *************************************************************************
ok: [web1]

TASK [Transfer Template To Web Server] *********************************************************
changed: [web1]

PLAY RECAP *************************************************************************************
web1                       : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


 ```
 3. update table.j2 
 
 old 
 ```
 $sql = "SELECT id, name, lastname, age FROM register  where age = 25 ";
 ```
 new 
 ```
 $sql = "SELECT id, name, lastname, age FROM register {% if PEOPLE_AGE is defined %} where age = {{ PEOPLE_AGE }} {% endif %}";
 ```
 3. goto browser and type `jenkins.local`
 ![jenkins local](https://github.com/jawad1989/Jenkins101/blob/master/images/20%20-%20jenkins%20local.PNG)
 
 4. now pass a parameter to ansible-playbook to get people with age of 25
 ```
ansible-playbook -i hosts people.yml -e "PEOPLE_AGE=25"

PLAY [web1] ************************************************************************************

TASK [Gathering Facts] *************************************************************************
ok: [web1]

TASK [Transfer Template To Web Server] *********************************************************
changed: [web1]

PLAY RECAP *************************************************************************************
web1                       : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


 ```
 5. goot browser and type `jenkins.local`
 ![jenkins local](https://github.com/jawad1989/Jenkins101/blob/master/images/21%20-%20jenkins%2025%20years.PNG)


 ## Create a jenkins job to run everything 
 1. Create a new job in jenkins
 2. add parameter `AGE`
 ![aGe](https://github.com/jawad1989/Jenkins101/blob/master/images/22%20-%20parametes.PNG)
 
 3. Colorize
 ![Color](https://github.com/jawad1989/Jenkins101/blob/master/images/22%20-b%20color.PNG)
 
 4. in build give path of play.yml and hosts(ansible inventory) file from jenkins container
 ![invetory](https://github.com/jawad1989/Jenkins101/blob/master/images/22%20-%20build.PNG)
 
 5. Save and build with params
 ![build](https://github.com/jawad1989/Jenkins101/blob/master/images/22%20-%20build%20with%20param.PNG)
 
 6. Check console logs
 ![Console](https://github.com/jawad1989/Jenkins101/blob/master/images/22%20-%20console%20output.PNG)
 
 7. goto browser to confirm the change
 ![browser](https://github.com/jawad1989/Jenkins101/blob/master/images/22%20-%20output%20table.PNG)
