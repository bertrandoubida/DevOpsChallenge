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

-After clicking sign in and landing on the "console home" page, look for the console search bar, type in "EC2" and hit enter.

-Once on the "EC2" page, look around the page for the key word "Instances" (usually located front and center on the page or in the left drop down menu), and click on it. To set up a new server, click on "Launch instances". 

-Once on the "Launch an instance" page, check the region name in the upper right corner to make sure you are in the desired location, choose a "Name" for your new server, select your "machine image" (I chose Ubuntu), the "instance type" (I chose t2.micro), use or create a "security group" that allows you to "SSH" into the server and also allows public access( "HTTPS prefered").

-Once done with the configurations, click on "Launch instance" to set up your new server.

-Once the instance is done being created, "running" and passed the "status check", select the instance, and click "connect" (located right above your new server's details). 

-Choose your prefered method of connecting, and officially connect into the server.



*Deploy the docker image to the server:




Run the checker script.
