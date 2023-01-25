# Copia Devops Technical Challenge

## Goal

Create a script that will build a docker image, deploy to server on aws, and create another script that will periodically check if the server is running.

###### Prerequisites

You will need an AWS account. Create one if you don't have one already. Use free-tier resources for this test.

###### The Challenge

You are required to set up a new server in AWS. You must:

** Build the docker image with the provided Dockerfile **
** Deploy the image to a server and have it be publicly accessible **
** The server application should return 200 OK when a http request is sent **



## Steps to Complete Work



###### Create the server:

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


###### Deploy the docker image to the server:

-Open a web browser and find the "DevOpsChallenge" repository on my github, copy the web address, head back to your EC2 server, make sure git is installed by typing "git --version" then hit enter

-Once confirmed that "git" is installed, type in "git clone" and paste the web address you copied from github earlier.

-When cloning is done, type in "ls" and hit enter to make sure the right repositorty was cloned
'''
-Install Docker on your server by running the following code one after the other, and hitting enter 
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
'''
- Type in "sudo docker info" and hit enter to verify that Docker has been installed on your server.

-Type in "cd DevOps" and press enter to open your repository.

-To build your docker image, simply type in "sudo docker build -t (any name you choose here) .".

-After letting the build finish, check that the build was made by typing in "sudo docker images" and hit enter

-Verify that your docker application engine is running (and get more details) by typing in "sudo systemctl status docker". Hit ":q" to exit viewing

-To deploy the docker image to the server type in "docker run -d -p 80:80 -name (your container name) (your image name)".

-If an error message appears, type in "sudo usermod -aG docker $USER", and restart your EC2 server and try ""docker run -d -p 80:80 -name (your container name) (your image name)" again.

-To check that your image is running on the server, type in "sudo docker ps".

-Check files in the container by typing in "docker exec -IT (Container name) Bash, after accessing container, enter "ls" to check.

-Check that the opened port works and that you have connection to your server by typing in "wget localhost".


###### Run the checker script.

-To check on the server, head back to your AWS console search bar, and type in "Lambda".

-Click on "Create a function", located on the right side of the page.

-At the top of the page, select "Author from scratch", input your function "name", chose "Python" for Runtime, expand the "Change default execution role" and select "Create a new role with basic Lambda permissions", click "Create function".

-Click on "Layers", located right under the image of your new function, "Add a layer", select the "Python" package as the layer and "add" to make sure your code has all the modules and is able to be understood by the system.

-Copy and paste this code into the section titled "Code Source" at the bottom of the page to replace the default code", this should allow you to check if the page is up running, and delivering content.

'''
import boto3
import requests

def lambda_handler(event, context):
    instance_id = event['instance_id']
    ec2 = boto3.client('ec2')
    response = ec2.describe_instances(InstanceIds=[put instance_id here])
    state = response['Reservations'][0]['Instances'][0]['State']['Name']

    if state == 'running':
        try:
            public_ip = response['Reservations'][0]['Instances'][0]['put PublicIpAddress here']
            response = requests.get(public_ip)
            if response.status_code == 200:
                return 'Instance is running and serving expected content'
            else:
                return 'Instance is running but not serving expected content'
        except requests.exceptions.RequestException as e:
            return 'Error: {}'.format(e)
    else:
        return 'Instance is not running'
        
        

import boto3
import requests

def lambda_handler(event, context):
    ec2 = boto3.client('ec2')
    response = ec2.describe_instances(InstanceIds=[put instance_id here])
    status = response['Reservations'][0]['Instances'][0]['State']['Name']
    if status == "running":
        try:
            r = requests.get("http://put PublicIpAddress here/")
            if r.status_code == 200:
                print("EC2 is running and serving content.")
            else:
                print("EC2 is running but not serving content.")
        except requests.exceptions.RequestException as e:
            print("EC2 is running but not serving content.")
    else:
        print("EC2 is not running.")
'''

-After entering the code in and deploying, make sure the lambda functions has the proper configurations by navigating to "Configuration"

-Click on "General configuration" on the left side of the page, edit the "Timeout" time to at least "15 sec"

-Click on "Triggers", "Add trigger", select "EventBridge" for a source, "create a new rule" that will trigger your lambda function every 5 minutes, click "add".

-Click on "VPC", associate all the neccesary resources (VPC, Subnet, Security Group), and save.

-Type in "IAM" in the main search bar to navigate to the IAM homepage.

-Click on "Roles" in the left dropdown to see your newly created lambda role, click on the lambda role, scroll down to add permissions to give it access to your EC2, cloudwatch and Internet.

-Adding "AmazonVPCCrossAccountNetworkInterfaceOperations", "CloudWatchLogsReadOnlyAccess", "CloudWatchEventsReadOnlyAccess", and "AmazonEC2ReadOnlyAccess" should be enough.

-Go back to the lamda homepage, and test your lambda function, it should prove successful.


###### Summary of events
** Create a VPC **
** Create an Ubuntu Instance **
** Build and deploy Docker **
** Create a Lambda Function **


###### To further automate:

####Create a template from Instance
- Navigate your way to  the instance homepage, select your instance, and click on "Actions", from the dropdown, click on "Image and templates", and click "Create template from instance".

-Create the template of an EC2 that will serve as a quick launch for the team, configurations of your created EC2 should be inputted already, so click "Create launch template".

-Now you can "Launch instance from template" when need be to save some time.

######Create a "Cloudformation" template from your instance


"Elastic Beanstalk" to create server, deploy Docker image to it, and provision health checks
Utilize ECR and ECS to register and manage containers 
"CodePipeline" to automate the entire process

###### Miscellaneous
-Second lambda function add just to check if website is working, returned 200ok but site is still a blank white page
