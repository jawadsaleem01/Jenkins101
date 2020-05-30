# Table of Contents:
1. installing jenkins in VM virtual Box ubuntu 18
   1. Download ubuntu image
   2. Setup VM in virtual box
   3. install Docker Engine
   4. Install Docker Compose
   5. Install Docker Jenkins Image
   6. Create Docker Compose for Jenkins
   7. Create a Docker Container for Jenkins
   8. Create a Local DNS for Jenkins
 2. Running first Job 
 3. Running first script from Job
 4. Parameterize Job
 10. install AWS CLI and mysql Client on docker
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

# 5. Boolean Parameter Job

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
# 6. Jenkins on Dockers

## 6.1. Docker + Jenkins + SSH

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


# 7. Install Plugin: ssh
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
  
# 8. Create a jenkins job using SSH 
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

# 9. Running MYSQL in docker

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
 
 # 10. install AWS CLI and mysql Client on docker
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
