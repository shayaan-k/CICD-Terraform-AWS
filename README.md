# NodeJS-AWS
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
