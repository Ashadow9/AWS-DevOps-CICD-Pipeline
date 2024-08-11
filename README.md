# AWS-DevOps-CICD-Pipeline

AWS DevOps CICD Pipeline
In This Project, we are Developing and Deploying a video streaming application on EC2 using Docker and AWS Developers Tools.

CodeCommit: For Source Code Management

CodeBuild: For building and testing our code in a serverless fashion

CodeDeploy: To deploy our code

CodePipeline: To streamline the CI/CD pipeline

System Manager: To store Parameters

DockerHub: To store Docker Images in a Repository

Identity and Access Management (IAM) for creating a Service Role

S3 for artifact storing

EC2 for Deployment


Project Architecture
![image](https://github.com/user-attachments/assets/1f0a0e04-e73e-4507-9504-77d3ca8fb25b)



Setting Up CodeCommit
Create a Repository

![image](https://github.com/user-attachments/assets/a92256e8-d8a1-4fa7-bf28-d70575741fcf)

Create IAM User:
Go to the IAM console and create a user.

Click on Create User -> User details -> Next.

Add Permission for full access to CodeCommit.
![image](https://github.com/user-attachments/assets/296fccbb-8d18-499e-88fa-ebbd35a648c9)

Click on Create for the user.

Click on the user and go to the security credentials section

Now we are going to create HTTPS Git credentials for AWS CodeCommit  for this user.

Go to the terminal and run this command

 git clone and the http url
Run this command now

Now we can connect to this repo.

add the index.html file in the repository

![image](https://github.com/user-attachments/assets/90d43cb7-f46f-4e3d-8381-43ddac5a411d)

![image](https://github.com/user-attachments/assets/22151527-a185-4dbb-9162-4dcda8d0f806)

![image](https://github.com/user-attachments/assets/4cc9ed2b-00e1-47df-92f3-6c97dd5690d0)


Setting Up CodeBuild
Click on Create build project

Follow this steps

![image](https://github.com/user-attachments/assets/1fcd4bf0-efc6-417f-bf6f-69b681555d33)

![image](https://github.com/user-attachments/assets/96ed1f8c-ec08-4e93-a742-850d44945ddd)
![image](https://github.com/user-attachments/assets/8237f010-7a77-412b-ad03-a986259d0992)
![image](https://github.com/user-attachments/assets/a2f4801b-5660-4e1f-84d5-9e133cebafea)

CodeBuild will need buildspec.yml to build a project.

We neec to do add The buildspec.yml file  in the repository root folder.

Also, This project will containerize so that select the Enable this flag if you want to build Docker images or want your builds to get elevated privileges.

![image](https://github.com/user-attachments/assets/20bac3e8-928c-44f3-8d30-9e581dde3f98)

In this project, we will build and push a Docker image to the DockerHub repository.

So, We need DockerHub credentials like Username and Password.

Also, we are using a free API to consume movie/TV data in this Project. TMDB.

Using AWS System Manager for storing secrets
Goto AWS System Manager dashboard.

Click on Parameter Store -> Create parameter
![image](https://github.com/user-attachments/assets/f21ef0b2-f494-4e4f-9e1d-1c1ecdb0f054)


In Parameter details

Add DockerHub Username

Name: /myapp/docker-credentials/username

Type: SecureString

Value: Add Your DockerHub Username

Add DockerHub Password

Name: /myapp/docker-credentials/password

Type: SecureString

Value: Add Your DockerHub Password or secret token

Add TMDB API Key

Name: /myapp/api/key

Type: SecureString

Value: Add Your TMDB API key
![image](https://github.com/user-attachments/assets/f6ccee5b-1b9a-4bc6-92eb-0e736838e68e)


Also, Add Permission in CodeBuild Created Role to assess Parameters from CodeBuild to System Manager

For this, Create an inline policy.

{
            "Sid": "Statement1",
            "Effect": "Allow",
            "Action": [
                "ssm:GetParameters"
            ],
            "Resource": [
                "arn:aws:ssm:*:{AWS Account ID 12 Digit}:parameter/*"
            ]
        }

![image](https://github.com/user-attachments/assets/23972186-3f99-48b5-b6ed-acbf64e011eb)









DockerHub Repository
![image](https://github.com/user-attachments/assets/f3d57b04-fd5e-4a36-ab7d-e3d844abc006)



Just for Test

Pull this Docker Image is locally using docker run -n netflix -p 8080:80 skywalkerdarth/netflix-react-app

![image](https://github.com/user-attachments/assets/40ea89e4-98ff-46f8-9056-8ac6b29ed09b)


Build Artifact store in S3 Bucket
In the CodeBuild console Click on Edit button -> Artifacts -> Type: “S3” -> put Uplode Location.
![image](https://github.com/user-attachments/assets/9aa8da2e-0df6-4284-870e-3dd82493e404)

Create CodeDeploy Application
Create Application and Compute platform is EC2/On-premises
Create Service role (Give permissions -

1.AmazonEC2FullAccess

2.AmazonEC2RoleforAWSCodeDeploy

3. AmazonS3FullAccess

4.AWSCodeDeployeFullAccess

5.AWSCodeDeployRole

6.AmazonEC2RoleforAWSCodeDeployLimitaccesstoS3



Create EC2 instance
Click Launch Instances

Amazon Linux -> t2.micro

Also, Create a Service Role for EC2 to access s3 & CodeDeploy

Goto IAM Dashboard -> Create Role -> Service Role -> EC2

Add this permission

AmazonEC2FullAccess

AmazonEC2RoleforAWSCodeDeploy

AmazonS3FullAccess

AWSCodeDeployFullAccess



Give Role name -> Click on Create Role


Give This Service Role here.


Add this Script to the User Data section.

Or Just run it manually.

For Amazon Linux


#!/bin/bash

sudo yum -y update

sudo yum install -y docker

sudo service docker start

sudo usermod -aG docker ec2-user

sudo yum -y install ruby

sudo yum -y install wget

cd /home/ec2-user

wget https://aws-codedeploy-ap-south-1.s3.ap-south-1.amazonaws.com/latest/install

sudo chmod +x ./install

sudo ./install auto

For Ubuntu


#!/bin/bash


sudo apt update

sudo install docker.io

sudo apt install ruby-full

wget cd /home/ubuntu wget https://aws-codedeploy-ap-south-1.s3.ap-south-1.amazonaws.com/latest/install

chmod +x ./install

sudo ./install auto

sudo service codedeploy-agent status



Create CodeDeploy Group
Create a CodeDeploy Group using the following Steps.

![image](https://github.com/user-attachments/assets/904d2dad-8af7-4345-8227-c2e27b85ab34)


![image](https://github.com/user-attachments/assets/861927f9-4b16-4c3b-86f0-1a8f90a7d039)

Click On Create Deployment

Start Deployment

![image](https://github.com/user-attachments/assets/e910807d-90ff-4889-a71b-3c1b4d0605af)


Create CodePipeline
Step 1: Choose pipeline setting -> PipelineName > Service role

Step 2: Add source stage -> CodeCommit > RepoName > BranchName > Select CodePipeline periodically for changes(For automation)

Step 3: Add build stage -> BuildProvider > Region > ProjectName > Single build

Step 4: Add deploy stage -> DeployProvider > Region > AppName > Deployment group

Step 5: Review

![image](https://github.com/user-attachments/assets/82a7eaec-026f-4c26-a081-b9ba6c95ebc0)


![image](https://github.com/user-attachments/assets/9791861c-3681-4a32-ad04-b3d8af252735)


CodeBuild History
![image](https://github.com/user-attachments/assets/2a8c62a8-7611-4e26-a011-35bb1c5cb7d5)


CodeDeploy

![image](https://github.com/user-attachments/assets/eae15fb6-a1ca-4db5-84a6-a073bcf996ae)


CodeDeploy History

![image](https://github.com/user-attachments/assets/7a8946e1-7a9b-423f-a1ea-6cc2809a6f6c)

Output
![image](https://github.com/user-attachments/assets/86f9d8e3-3671-4ff9-b1e5-56dff921c9a1)

