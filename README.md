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

# installing jenkins in VM virtual Box ubuntu 18
 ### 1. Download ubunut image
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

 ### Create a Docker Container for Jenkins
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
 
 ### Create a Local DNS for Jenkins
 
 * Open notepad with admin rights
 * goto `C:\Windows\System32\drivers\etc`
 * enter your ip in our case its `192.168.56.104 jenkins.local`
 * goto brower and type `jenkins.local:8080` this will bring up the jenkins

![jenkins dns](https://github.com/jawad1989/Jenkins101/blob/master/images/11%20-%20local%20dns.PNG)
