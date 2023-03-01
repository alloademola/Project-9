# Project-9

Continous Integration Pipeline For Tooling Website

TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS

In our previous Project 8, we introduced horizontal scalability concept,
which allow us to add new Web Servers to our Tooling Website 
and we have successfully deployed a set up with 2 Web Servers 
and also a Load Balancer to distribute traffic between them. 
so If it is just two or three servers – it is not a big deal to configure them manually. 
but Imagine that you would need to repeat the same task over and over again adding dozens or even hundreds of servers.

DevOps is about Agility, and speedy release of software and web solutions. 
One of the ways to guarantee fast and repeatable deployments is Automation of routine tasks.

But In this project we are going to start automating part of our routine 
tasks with a free and open source automation server – Jenkins. 
It is one of the mostl popular CI/CD tools, 
it was created by a former Sun Microsystems developer Kohsuke Kawaguchi 
and the project originally had a named "Hudson".

Acording to Circle CI, Continuous integration (CI) is a software development strategy
that increases the speed of development while ensuring the quality of 
the code that teams deploy. 
Developers continually commit code in small increments (at least daily, or even several times a day), 
which is then automatically built and tested before it is merged with the shared repository.

So in this project, we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub https://github.com/<yourname>/tooling will be automatically be updated to the Tooling Website.


Task

Enhance the architecture prepared in Project 8 by adding a Jenkins server, 
configure a job to automatically deploy source codes changes from Git to NFS server.

Below diagram show how our updated architecture will look like upon competion of this project:

<img width="796" alt="Screenshot 2023-03-01 at 1 30 50 AM" src="https://user-images.githubusercontent.com/118350020/222014479-4e942559-4f51-4dbd-b4f7-592df2c848d2.png">

Step 1

  
INSTALL AND CONFIGURE JENKINS SERVER  
  
Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins" 
as shown in the below diagram


<img width="843" alt="Screenshot 2023-03-01 at 3 57 18 AM" src="https://user-images.githubusercontent.com/118350020/222033833-301847a5-278a-4d02-93b2-e264fce1fc73.png">


After creating and connecting to our Jenkins 
we need to Install JDK (since Jenkins is a Java-based application)

So we are going to run the two command below,one after each other

sudo apt update
sudo apt install default-jdk-headless -y
  
as shown in the below diagram

  
<img width="839" alt="Screenshot 2023-03-01 at 4 01 56 AM" src="https://user-images.githubusercontent.com/118350020/222034371-93bb588b-1c47-4317-a78f-b9266e00369c.png">

 next is to install Install Jenkins, so we are going to run this command below
  
 wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
  
as shown in the below diagram


<img width="842" alt="Screenshot 2023-03-01 at 4 12 02 AM" src="https://user-images.githubusercontent.com/118350020/222035610-937a8b40-b6d1-43c9-b1e8-77ab3ca4bb2b.png">
<img width="838" alt="Screenshot 2023-03-01 at 4 11 08 AM" src="https://user-images.githubusercontent.com/118350020/222035649-fadb4dde-2dee-4faa-8fa9-c2b8d73bfc94.png">

so make sure Jenkins is up and running by running the command below
sudo systemctl status jenkins
  
<img width="820" alt="Screenshot 2023-03-01 at 4 15 42 AM" src="https://user-images.githubusercontent.com/118350020/222036085-c572cd84-b974-45a4-befc-2137d9490a65.png">

By default Jenkins server uses TCP port 8080 – 
So we need to open it by 
creating a new Inbound Rule in our EC2 Security Group

so after that, we also need to Perform initial Jenkins setup.
From your browser let us
access http://54.93.222.120:8080 as shown in the below diagram


<img width="1440" alt="Screenshot 2023-03-01 at 4 26 21 AM" src="https://user-images.githubusercontent.com/118350020/222037487-3d96ce41-a880-4679-a477-c8440cc97c68.png">


  

You will be prompted to provide a default admin password   
so we are going to Retrieve it from our server:
using this command below

sudo cat /var/lib/jenkins/secrets/initialAdminPassword
the password is what is highlighted in the below diagram
  
<img width="831" alt="Screenshot 2023-03-01 at 4 30 03 AM" src="https://user-images.githubusercontent.com/118350020/222038013-0bd76b20-e04f-4c7d-89b2-4b4186c38d63.png">

<img width="1440" alt="Screenshot 2023-03-01 at 4 31 52 AM" src="https://user-images.githubusercontent.com/118350020/222038280-9c3d9ceb-f906-4bc7-88e6-e5b64304285b.png">
  
  

