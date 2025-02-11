# CICD with Terraform on AWS
Using Terraform, Docker, Github Actions and AWS to build a complete CI/CD pipeline

## Node JS app
Start with a basic node js app\
run ```npm init -y```\
create an app.js file\
import express\
open a port\
send response message

## Dockerfile
I need to create a Docker file that can run this node js app anywhere

Begin by giving it an environment with node already installed.\
Copy over the packages.json file and run ```npm install``` to install the dependencies\
Copy the rest of the project files, expose port 8080 as specified in the app and run the app.

## Terraform
### Provider
Start by creating a terraform file
This project will use AWS so I use an aws provider
I also initialized a backend to hold the terraform state. This backend will be a s3 bucket

### EC2 Instance
Initialize EC2 instance to use ubuntu image.
Pass a key name wpecified in the aws_key_pair resource
Define a connection of ssh sfor ubuntu user

### Security group
Need a security group to allow connections via ssh and http
Define a security group resource and set egress (outbound) values to allow all
Set Ingress (inbound) values to allow port 22 and port 80

### IAM role
Need to define IAM
Need our instance to connect to our ECR to pull docker image
Go to AWS dashboard - > IAM Roles -> Create a new role
Give the new rol ECR Container Read only permission
Attach that role to the instance

### Define an output
need instance public ip to log in via ssh

### Create Variables file to store values
Write default values to be used by github actions to deploy the EC2 Instance

## Github Actions
### Add secrets
The secrets that need to be added are:
1. AWS access key 
2. AWS access key private
3. AWS Bucket name
4. AWS SSH Public key
5. AWS SSH Private key

### Creating deploy infrastructure workflow
The github action first sets the environment variables as those found in the repository secrets
I then start my job which consists of 4 steps:
1. Setup Terraform. There is already a hashicorp action for this so I can just use that.
2. Run Terraform Init: Tells Terraform to install neccessary plugins to setup server. As well as bucket and region config
3. Run Terraform Plan: This is a rough run of the script. It can be used to catch errors ahead of time
4. Terraform apply. Actually applies the changes. Is based on the output from plan to ensure it only runs if terraform plan is successful

pushing this code to remote should automatically trigger the jobs and deploy.

## Creating deploy app workflow
In order to deploy the app we need to get the public ip address of the server we create.
Adding a method of getting that IP is needed and then it needs to be passed to the next job.

The deploy app job sets the IP address as an environment variable.
We then complete a few setup steps.
1. Login to Amazon ecr using the amazon aws builtin action
2. Create a repository in ECR for our app
3. Build the docker image by passing the registry name (collected from the login command), the repo name (repo we created), and the image tag (in this case it is the commit hash)

Finally, we cna deploy our image onto the EC2 instance
Using the ssh-actions step, we can ssh into our instance by passing the hostname, public ip, and ssh key.
While sshing, we can run a script that install docker and awscli, stops any running containers, pulls the latest image of our docker application, and runs it.

## Checking results
Once our github action is complete. Terraform should have setup a server and ran our containerized app on it.
By navigating to the instances IP address on the browser. We can see the node app running. The output "Service is up and running" is shows which means everything went fine.