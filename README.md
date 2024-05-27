# City Direction Web App Deployment on AWS

Welcome to the City Direction Web App Deployment project repository! This repository contains the code and resources needed to deploy a static web application providing directions between cities on Amazon Web Services (AWS). 

## Project Structure

- Part-1: CodeCommit Setup
  - Contains scripts and resources for setting up version control using AWS CodeCommit.

- Part-2: CodeBuild Implementation
  - Includes configurations and files for automating builds with AWS CodeBuild.

- Part-3: CodeDeploy and Pipeline Automation
  - Contains deployment scripts and instructions for automating deployments with AWS CodeDeploy and CodePipeline.
  
## How to Clone this Repository

To clone this repository to your local machine, follow these steps:

1. Open your terminal or command prompt.
2. Navigate to the directory where you want to clone the repository.
3. Use the following command to clone the repository:

```bash
git clone (https://github.com/TheMannu/Project-AWS-Devops.git)
```


## How to Create a CI/CD Pipeline for a Static Web Application Using AWS DevOps Services

### Introduction

In this project we will create a CI/CD pipeline for your static web application using AWS DevOps-related services.

### Overview
Our project involves a simple static web application that finds routes between cities using Google Maps. The goal is to automate its deployment on AWS using CodePipeline, CodeCommit, CodeBuild, and CodeDeploy.

### Architecture
1. **CodeCommit**: AWS version control service where we'll push our local code.
2. **CodeBuild**: Takes the code from CodeCommit, builds it, and stores artifacts in an S3 bucket.
3. **S3**: Stores build artifacts.
4. **CodeDeploy**: Deploys the application on an EC2 instance.

### Prerequisites
1. An AWS account.
2. An IAM user with necessary permissions.

### Part 1: Setting Up CodeCommit
1. **Create a CodeCommit Repository**:
   - Go to AWS Console > CodeCommit > Create Repository.
   - Name it `my-direction-app`.
   - Optional: Add description and tags.

2. **Push Local Files to CodeCommit**:
   - Initialize a Git repository locally: `git init`.
   - Add remote: `git remote add origin <CodeCommit repository URL>`.
   - Push code: 
     ```bash
     git add .
     git commit -m "Initial commit"
     git push origin master
     ```

### Part 2: Building the Project with CodeBuild
1. **Create an S3 Bucket**:
   - Name it `my-direction-app-bucket`.

2. **Create a Build Project in CodeBuild**:
   - Go to AWS Console > CodeBuild > Create Build Project.
   - Name it `my-direction-app`.
   - Choose `AWS CodeCommit` as the source, select your repository and branch.
   - Environment: Use a managed image (Ubuntu standard).
   - Service role: Create a new role or use an existing one with necessary permissions.

3. **Buildspec.yml**:
   ```yaml
   version: 0.2
   phases:
     install:
       commands:
         - sudo apt-get update -y
         - sudo apt-get install -y nginx
     build:
       commands:
         - sudo cp index.html /var/www/html/
     post_build:
       commands:
         - echo "Build completed"
   artifacts:
     files:
       - '**/*'
   ```

4. **Start the Build**:
   - Go to CodeBuild and start the build.
   - Check the build logs to ensure it completes successfully.

### Part 3: Deploying with CodeDeploy
1. **Create an EC2 Instance**:
   - Name it `my-direction-app-instance`.
   - Choose an Amazon Linux 2 AMI.
   - Assign an IAM role with CodeDeploy permissions.

2. **Create a CodeDeploy Application and Deployment Group**:
   - Go to CodeDeploy > Create Application > Name it `my-direction-app`.
   - Create a deployment group, select the EC2 instance.

3. **Appspec.yml**:
   ```yaml
   version: 0.0
   os: linux
   files:
     - source: /
       destination: /var/www/html/
   hooks:
     BeforeInstall:
       - location: scripts/install_dependencies.sh
         timeout: 300
         runas: root
     AfterInstall:
       - location: scripts/start_server.sh
         timeout: 300
         runas: root
   ```

4. **Deployment Scripts**:
   - `install_dependencies.sh`:
     ```bash
     #!/bin/bash
     sudo apt-get update -y
     sudo apt-get install -y nginx
     ```
   - `start_server.sh`:
     ```bash
     #!/bin/bash
     sudo service nginx start
     ```

5. **Deploy the Application**:
   - Create a deployment in CodeDeploy using the artifacts from S3.
   - Verify the deployment by accessing the EC2 instance's public IP.

### Automating with CodePipeline
1. **Create a CodePipeline**:
   - Go to CodePipeline > Create Pipeline.
   - Name it `my-direction-app-pipeline`.
   - Choose source provider `AWS CodeCommit`, build provider `AWS CodeBuild`, and deploy provider `AWS CodeDeploy`.
   - Configure each stage to use the appropriate services and branches.

2. **Pipeline Execution Modes**:
   - **Superseded**: Newer executions overtake older ones.
   - **Queued**: Executions occur sequentially.
   - **Parallel**: Executions run simultaneously.

3. **Start the Pipeline**:
   - Check the pipeline stages (Source, Build, Deploy) for successful execution.
   - Ensure the web application is deployed and accessible.

### Conclusion

We've successfully created a CI/CD pipeline for a static web application using AWS DevOps services. This pipeline automates the deployment process, ensuring consistent and reliable deployments. If you enjoyed this tutorial, please like and subscribe to Dosmaster for more AWS and DevOps content!
