# Copia Devops Technical Challenge

# Goal

Create a script that will build a docker image, deploy to server on aws, and create another script that will periodically check if the server is running.

# Prerequisites

You will need an AWS account. Create one if you don't have one already. Use free-tier resources for this test.

# The Challenge

You are required to set up a new server in AWS. You must:

* Build the docker image with the provided Dockerfile
* Deploy the image to a server and have it be publicly accessible
* The server application should return 200 OK when a http request is sent



#Steps to Complete Work



*Create the server:

-Open a web browser, navigate to "aws.amazon.com"

-Click "Sign in to console" on the upper right corner, if not a root user, enter your "Account ID" (12 digits), your "username", and your "password".

-After clicking sign in and landing on the "console home" page, look for the console search bar, type in "VPC", and click to head to the "VPC home page"

-Click on "Create VPC", when new creation page appears, select "VPC and more", and "Create VPC".

-Look for the console search bar, type in "EC2" and hit enter.

-Once on the "EC2" page, look around the page for the key word "Instances" (usually located front and center on the page or in the left drop down menu), and click on it. To set up a new server, click on "Launch instances". 

-Once on the "Launch an instance" page, check the region name in the upper right corner to make sure you are in the desired location, choose a "Name" for your new server, select your "machine image" (I chose Ubuntu), the "instance type" (I chose t2.micro), select your newly created "VPC" and "public subnet", use or create a "security group" that allows you to "SSH" into the server from "My IP" and also allows public access (HTTP & HTTPS, enable "Auto-assign public IP").

-Once done with the configurations, click on "Launch instance" to set up your new server.

-Once the instance is done being created, "running" and passed the "status check", select the instance, and click "connect" (located right above your new server's details). 

-Choose your prefered method of connecting, and officially connect into the server.



*Deploy the docker image to the server:

-Open a web browser and find the "DevOpsChallenge" repository on my github, copy the web address, head back to your EC2 server, make sure git is installed by typing "git --version" then hit enter

-Once confirmed that "git" is installed, type in "git clone" and paste the web address you copied from github earlier.

-When cloning is done, type in "ls" and hit enter to make sure the right repositorty was cloned

-Install Docker on your server by running the following code one after the other, and hitting enter 
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io

- Type in "sudo docker info" and hit enter to verify that Docker has been installed on your server

-Type in "cd DevOps" and press enter to open your repository.

-To build your docker image, simply type in "sudo docker build -t (any name you choose here) ."

-After letting the build finish, check that the build was made by typing in "sudo docker images" and hit enter

-Verify that your docker application engine is running (and get more details) by typing in "sudo systemctl status docker". Hit ":q" to exit viewing

-To deploy the docker image to the server type in "docker run -d -p 80:80 (your image name)".

-If an error message appears, type in "sudo usermod -aG docker $USER", and restart your EC2 server and try ""docker run -d -p 80:80 (your image name)" again.

-To check that your image is running on the server, type in "sudo docker ps".



*Run the checker script.

-To check on the server, head back to your AWS console search bar, and type in "Lambda".

-Click on "Create a function", located on the right side of the page

-At the top of the page, select "Author from scratch", input your function "name", chose "Python" for Runtime, expand the "Change default execution role" and select "Create a new role with basic Lambda permissions", click "Create function".

-Copy and paste this code into the section titled "Code Source" at the bottom of the page to replace the default code".

#!/bin/bash

#Check if the server is up and serving the expected content
status_code=$(curl -s -o /dev/null -w "%{http_code}" http://your-server-ip-or-domain)

if [ "$status_code" -eq "200" ]; then
    echo "Server is up and serving the expected content"
else
    echo "Server is down or not serving the expected content"
fi

-Enter you EC2 "Public-IP", click on "deploy" (located next to "Test")




#Summary of events
Create a VPC
Creat an Ubuntu Instance
Build and deploy Docker
Create a Lambda Function

*To further automate:
Create a "Cloudformation" template from your instance
"Elastic Beanstalk" to create server, deploy Docker image to it, and provision health checks
"CodePipeline" to automate the entire process
