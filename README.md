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

