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


Once plugins installation is done 
Next is to create an admin user and you will get your Jenkins server address.  
As shown in the below diagrams

 

<img width="1440" alt="Screenshot 2023-03-01 at 4 38 31 AM" src="https://user-images.githubusercontent.com/118350020/222042015-4bcbbe3e-14eb-4c77-9bbe-350bcf7b7e7e.png">
<img width="1440" alt="Screenshot 2023-03-01 at 4 39 27 AM" src="https://user-images.githubusercontent.com/118350020/222042062-e9b49cc3-8318-4fe3-a934-ca9222221192.png">
<img width="1440" alt="Screenshot 2023-03-01 at 4 39 54 AM" src="https://user-images.githubusercontent.com/118350020/222042164-f891ef87-e317-42c5-88a8-c8e4f5b7681a.png">
<img width="1440" alt="Screenshot 2023-03-01 at 4 41 53 AM" src="https://user-images.githubusercontent.com/118350020/222042297-7fb3deed-b182-4e2f-a4b5-09627227b765.png">
<img width="1440" alt="Screenshot 2023-03-01 at 4 42 02 AM" src="https://user-images.githubusercontent.com/118350020/222042341-c2b5ad18-f9c2-4bb2-9fde-ef204cc59a98.png">

The installation is completed!

Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks
In this part, we are going to learn how to configure a simple Jenkins job/project 
(these two terms can be used interchangeably). 
  
This job will be triggered by GitHub webhooks and will execute
a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.  

  
first thing is to enable webhooks in your GitHub repository settings as shown in the below diagrams
<img width="1440" alt="Screenshot 2023-03-01 at 5 16 36 AM" src="https://user-images.githubusercontent.com/118350020/222044463-44f246bc-2864-46c6-b88e-b2613be3033c.png">
<img width="1440" alt="Screenshot 2023-03-01 at 5 16 57 AM" src="https://user-images.githubusercontent.com/118350020/222044547-7474d621-9c58-4994-bde6-7f7c1680b992.png">
<img width="1440" alt="Screenshot 2023-03-01 at 5 17 18 AM" src="https://user-images.githubusercontent.com/118350020/222044565-02ee84d0-5f1b-4e6a-a4a7-6107d8a0c124.png">
<img width="1440" alt="Screenshot 2023-03-01 at 5 17 55 AM" src="https://user-images.githubusercontent.com/118350020/222044584-6c4f2900-4b5f-46ce-8598-f7f9c9fdf576.png">
<img width="1440" alt="Screenshot 2023-03-01 at 5 21 10 AM" src="https://user-images.githubusercontent.com/118350020/222044608-fe875be0-36d3-49a5-9001-f98543f6627d.png">

Second thing to do now is to
Go to Jenkins web console, click "New Item" and create a "Freestyle project"    
as shown in the below diagram

<img width="1440" alt="Screenshot 2023-03-01 at 5 27 28 AM" src="https://user-images.githubusercontent.com/118350020/222044852-6df71deb-dca0-4fd6-8d1e-6269b35a166a.png">
  
<img width="1440" alt="Screenshot 2023-03-01 at 5 27 34 AM" src="https://user-images.githubusercontent.com/118350020/222044870-af65d67a-b413-4ea2-8f38-51ebb2d19021.png">
  
<img width="1440" alt="Screenshot 2023-03-01 at 5 30 30 AM" src="https://user-images.githubusercontent.com/118350020/222045171-22a31f7e-8cf5-46a5-b3d6-61390501027a.png">
<img width="1440" alt="Screenshot 2023-03-01 at 5 31 22 AM" src="https://user-images.githubusercontent.com/118350020/222045280-d722b70a-8aa0-45c2-8bf7-98f81bd72581.png">

<img width="1440" alt="Screenshot 2023-03-01 at 6 00 04 AM" src="https://user-images.githubusercontent.com/118350020/222049048-c0918a3c-025f-4506-8b00-b35bc0ae6b39.png">
<img width="1440" alt="Screenshot 2023-03-01 at 6 00 18 AM" src="https://user-images.githubusercontent.com/118350020/222049061-4b8f10dd-04a2-49be-9657-1b6907beec4a.png">
<img width="1440" alt="Screenshot 2023-03-01 at 6 00 24 AM" src="https://user-images.githubusercontent.com/118350020/222049100-11c72dd1-d063-4078-8a61-05d2b171c3e9.png">
  
So next step is to Save the configuration 
so let us try to run the build. For now we can only do it manually.
Click "Build Now" button, if you have configured everything correctly, the build will be successfull and you will see it under #1
  
as shown in the below diagram

  
<img width="1440" alt="Screenshot 2023-03-08 at 9 47 12 PM" src="https://user-images.githubusercontent.com/118350020/223846483-c71bbb87-26a5-463a-86fb-32fb608e7cf2.png">

Now let us open the build and check in "Console Output" if it has run successfully.
as shown in the below diagram

<img width="1440" alt="Screenshot 2023-03-08 at 9 55 14 PM" src="https://user-images.githubusercontent.com/118350020/223847802-f318b07c-dc76-482a-a154-0e9e9198dd58.png">

Our build does not produce anything and it runs only when we trigger it manually
So we are going to fix it.

So Click "Configure" your job/project and add these two configurations
Configure triggering the job from GitHub webhook:

As shown in the below diagram below

<img width="1440" alt="Screenshot 2023-03-08 at 9 59 20 PM" src="https://user-images.githubusercontent.com/118350020/223848581-18646378-bb82-4288-8725-98964ea5edfc.png">

<img width="1440" alt="Screenshot 2023-03-08 at 10 00 37 PM" src="https://user-images.githubusercontent.com/118350020/223848932-770048fc-3fec-45e4-8679-001766713c7d.png">
<img width="1440" alt="Screenshot 2023-03-08 at 10 02 25 PM" src="https://user-images.githubusercontent.com/118350020/223849206-b078ac58-e3c6-48df-bc2c-1414627e2d04.png">
<img width="1440" alt="Screenshot 2023-03-08 at 10 03 33 PM" src="https://user-images.githubusercontent.com/118350020/223849478-7cd41390-513b-460c-afd2-371d6b3ea021.png">

so we are going to click on the save option in the above diagram.
the we now go back to our tooling on github
 
<img width="1440" alt="Screenshot 2023-03-08 at 10 09 02 PM" src="https://user-images.githubusercontent.com/118350020/223850929-3fdafdb2-421f-42ac-acff-c40b0574a5a9.png">
<img width="1440" alt="Screenshot 2023-03-08 at 10 11 30 PM" src="https://user-images.githubusercontent.com/118350020/223851060-96d78b0f-ebe8-4111-a9c0-4844c96b0528.png">

we just add a line at the ending , and commit changes

<img width="1440" alt="Screenshot 2023-03-08 at 10 14 42 PM" src="https://user-images.githubusercontent.com/118350020/223851745-fee67c15-fb33-4273-8660-cf107f7b8c9d.png">

<img width="1440" alt="Screenshot 2023-03-08 at 10 17 02 PM" src="https://user-images.githubusercontent.com/118350020/223852187-34ffee5c-8568-4505-8c39-d89d8caf82d3.png">
 
as you can see from the above diagram,the commment we add also trigger on the Jenkins.
now let us click on the status and see the build Artifacts as shown in the below diagram
  
<img width="1440" alt="Screenshot 2023-03-08 at 10 18 49 PM" src="https://user-images.githubusercontent.com/118350020/223852751-ebbb41fe-d541-4372-acea-8047454ac9cc.png">

  
we have now configured an automated Jenkins job that receives files from GitHub by webhook trigger 
(this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub).
There are also other methods: 
trigger one job (downstream) from another (upstream), poll GitHub periodically and others.
  
ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/
  
next step is to Configure Jenkins to copy files to NFS server via SSH

Now we have our artifacts saved locally on Jenkins server, 
the next step is to copy them to our NFS server to /mnt/apps directory.

Jenkins is a highly extendable application and there are 1400+ plugins available.
We will need a plugin that is called "Publish Over SSH".

first thing to do is
Install "Publish Over SSH" plugin.
On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item.

On "Available" tab search for "Publish Over SSH" plugin and install it

as shown in the below diagram

  
<img width="1440" alt="Screenshot 2023-03-08 at 10 41 50 PM" src="https://user-images.githubusercontent.com/118350020/223856892-b667ec75-3eb5-425a-ab34-1568f15d6955.png">
<img width="1440" alt="Screenshot 2023-03-08 at 10 42 49 PM" src="https://user-images.githubusercontent.com/118350020/223857139-93f34ff8-6f3e-4693-9718-1d870a61e70d.png">
<img width="1440" alt="Screenshot 2023-03-08 at 10 45 53 PM" src="https://user-images.githubusercontent.com/118350020/223857708-e2249cad-e741-4e1b-8aab-4bfb2ef73415.png">
<img width="1440" alt="Screenshot 2023-03-08 at 10 46 57 PM" src="https://user-images.githubusercontent.com/118350020/223857851-0e751cb4-c8f2-4da2-8291-611a76b05003.png">

as shown in the below diagram, the SSH as being downloaded successfully 

<img width="1440" alt="Screenshot 2023-03-08 at 10 48 44 PM" src="https://user-images.githubusercontent.com/118350020/223858422-67fc230d-2d07-42d3-a184-319cb1e67bda.png">

Second step is to Configure the job/project to copy artifacts over to NFS server
So On our main dashboard, you will  select "Manage Jenkins" and choose "Configure System" menu item.
As shown in the below diagram
  
<img width="1440" alt="Screenshot 2023-03-08 at 11 15 46 PM" src="https://user-images.githubusercontent.com/118350020/223863641-84e05bed-c453-41c6-88a6-efbacdef9d47.png">

So On our main dashboard, you will  select "Manage Jenkins" and choose "Configure System" menu item.
  
<img width="1440" alt="Screenshot 2023-03-08 at 11 16 56 PM" src="https://user-images.githubusercontent.com/118350020/223863918-018ce59d-9c1c-46b3-b91d-9bd0f36afc40.png">
  
So you will Scroll down to Publish over SSH plugin configuration section 
and configure it to be able to connect to your NFS server
  
  
<img width="1440" alt="Screenshot 2023-03-08 at 11 21 10 PM" src="https://user-images.githubusercontent.com/118350020/223864643-5b6f9265-bb24-481f-8f63-4a59dfb76132.png">

and configure it to be able to connect to your NFS server
