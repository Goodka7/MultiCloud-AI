<p align="center"><img src="https://github.com/user-attachments/assets/c584d492-45a7-4964-b7ea-c9d7e13eba49" width="500" />
<br>
<img src="https://github.com/user-attachments/assets/23065407-8251-467c-b7dd-17e617b8832f" width="500" /></p>

# Table of Contents
- [Overview](#overview)
- [Technologies and Tools Leveraged](#technologies-and-tools-leveraged)
- [STAGE 1: Foundational Services Setup](#stage-1---foundational-services-setup)
  - [AWS Virtual Machine Setup](#aws-virtual-machine-setup)
  - [Terraform Setup](#terraform-setup)
  - [DynamoDB Setup](#dynamodb-setup)
- [STAGE 2: Containers Setup](#stage-2---containers-setup)
  - [Docker Setup](#docker-setup)
  - [Kubernetes Setup](#kubernetes-setup)
  - [EKS Setup](#eks-setup)
  - [ECR Setup](#ecr-setup)
  - [STAGE 2: Wrap-Up](#stage-2---wrap-up)
- [STAGE 3: Pipeline (Automation) Setup](#stage-3---pipeline-automation-setup)
  - [GitHub Setup](#github-setup)
  - [CodePipeline Setup](#codepipeline-setup)
  - [STAGE 3: Wrap-Up](#stage-3---wrap-up)
- [STAGE 4: AI Assistant Setup](#stage-4---ai-assistant-setup)
  - [Lambda Setup](#lambda-setup)
  - [Bedrock Agent Setup](#bedrock-agent-setup)
  - [OpenAI Agent Setup](#openai-agent-setup)
  - [STAGE 4: Wrap-Up](#stage-4---wrap-up)
- [STAGE 5: MultiCloud Setup](#stage-5---multicloud-setup)
  - [BigQuery Setup](#bigquery-setup)
  - [Azuer Language Setup](#azure-language-setup)
  - [STAGE 5: Wrap-Up](#stage-5---wrap-up)
- [Lessons Learned](#lessons-learned)
---

## Overview:

This project involves setting up a comprehensive system to manage an e-commerce platform, **CloudMart**, using multiple cloud services, containerization, automation, and AI integration. The setup is divided into several stages, each focused on different infrastructure and application components.

1. **Foundational Services Setup**:
   - **AWS Virtual Machine Setup**: Provisioning an EC2 instance for hosting the platform.
   - **Terraform Setup**: Automating infrastructure setup with Terraform, including provisioning S3 buckets and DynamoDB tables.
   - **DynamoDB Setup**: Automating the creation and management of DynamoDB tables for data storage.

2. **Containers Setup**:
   - **Docker Setup**: Setting up containerized environments for both back-end and front-end services.
   - **Kubernetes Setup**: Configuring Kubernetes clusters and integrating them with EKS for managing the containers.
   - **ECR Setup**: Storing Docker images in Amazon ECR and using Kubernetes to manage deployments.

3. **Pipeline (Automation) Setup**:
   - **GitHub Setup**: Setting up GitHub repositories for source control.
   - **CodePipeline Setup**: Automating the build and deployment process using AWS CodePipeline to integrate continuous delivery.

4. **AI Assistant Setup**:
   - **Lambda Setup**: Implementing AWS Lambda functions for processing tasks such as listing products.
   - **Bedrock Agent and OpenAI Setup**: Integrating AI assistants to handle product recommendations and customer support using Bedrock and OpenAI models.

5. **MultiCloud Setup**:
   - **BigQuery Setup**: Setting up Google Cloud BigQuery for data analytics and integrating it with AWS Lambda to transfer data.
   - **Azure Language Setup**: Configuring sentiment analysis on customer support tickets using Azure’s Language API.

Each stage contributes to building a robust, automated system that supports product recommendations, customer inquiries, and back-end infrastructure management. The goal is to provide a scalable and efficient environment for CloudMart’s operations, with integrated AI for enhanced user experience and sentiment analysis.

---
## Technologies and Tools Leveraged

| **Type**             | **Tools/Technologies**                                                               |
|----------------------|--------------------------------------------------------------------------------------|
| **Cloud Platforms**   | AWS (EC2, S3, DynamoDB, EKS, Lambda, CodePipeline, ECR), Google Cloud (BigQuery), Azure (Language)|
| **Containerization**  | Docker, Kubernetes, EKS, ECR                                                         |
| **Infrastructure**    | Terraform, AWS IAM, kubectl, eksctl, AWS ES2 CLI                                     |
| **CI/CD**             | GitHub, GitHub Actions, CodePipeline                                                |
| **Scripting**         | Bash, Node.js, Git, Shell, Markdown, HTML                                            |
| **AI & ML**           | Bedrock AI, OpenAI GPT-4, Azure Language API (Sentiment Analysis)                    |
| **Databases**         | DynamoDB, BigQuery                                                                   |
| **Security**          | IAM Roles, AWS Lambda Permissions                                                   |
| **Dev Tools**         | Visual Studio Code, Nano, Git                                                       |

---

# STAGE 1 - Foundational Services Setup

## AWS Virtual Machine Setup

### First I provisioned a VM using EC2

<img src="https://github.com/user-attachments/assets/f6f8e75c-e9a4-46cd-b480-c0cac552cb6c" width="800" />

### Next, I launched the EC2 Instance to make use of the CLI

<img src="https://github.com/user-attachments/assets/1c208424-1f25-4ebf-ae0b-770db497a741" width="800" />

[Back to the top](#table-of-contents)
---

## Terraform Setup

### Using the CLI, I installed Terraform on my EC2 VM

<img src="https://github.com/user-attachments/assets/1907d4ad-3a76-4100-88b4-5b7cd224255d" width="800" />

### I then used nano to create a config for a S3 bucket called `main.tf`:

```
provider "aws" {
  region = "us-west-2"  # Replace with your desired region
}

resource "random_id" "bucket_suffix" {
  byte_length = 8
}

resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-unique-bucket-name-${random_id.bucket_suffix.hex}"

  tags = {
    Name        = "My bucket"
    Environment = "Dev"
  }
}
```

### I used `terraform init` to initialize Terraform.

<img src="https://github.com/user-attachments/assets/aabee7cc-d647-4064-a3ad-3e7d78b10ec0" width="800" />

### I then used `terraform plan` to review and make sure the bucket is configured correctly

<img src="https://github.com/user-attachments/assets/9e9dca6f-6162-4923-9a35-9ce30f66dddd" width="800" />

### Next I used `terraform apply` to automate the S3 bucket creation

<img src="https://github.com/user-attachments/assets/564daa6a-4377-4b7d-b945-74ea189c95e6" width="800" />

### After that, I manually verified in S3 that the bucket was created

<img src="https://github.com/user-attachments/assets/7ba5e91f-c59a-4bfb-afe5-1cfc37e73bdd" width="800" />

---

> **Note:** The S3 bucket was set up to verify Terraform's configuration with AWS, test basic resource provisioning with a simple service, and ensure that resources were created correctly with the appropriate IAM permissions before moving on to the more complex DynamoDB setup.

---
[Back to the top](#table-of-contents)
---

## DynamoDB Setup

### I edited the `main.tf` file so I can automate the creation of DynamoDB tables

<img src="https://github.com/user-attachments/assets/f4697f46-cbcf-4b3b-86d3-5fce97c07e4a" width="800" />

### Using `terraform apply` I automated the table creation and removed the S3 bucket

<img src="https://github.com/user-attachments/assets/1fa6b58c-7a64-412a-a469-f6f5da42939d" width="800" />

[Back to the top](#table-of-contents)
---

# STAGE 2 - Containers Setup

## Docker Setup

### I used the CLI on EC2 to install Docker

<img src="https://github.com/user-attachments/assets/5af95d6c-33c4-463a-880c-db0b969e4adf" width="800" />
<img src="https://github.com/user-attachments/assets/5786f4d6-03d9-4e12-8ddb-75ffe13658cb" width="800" />

### I created a new directory for the back-end using the CLI

<img src="https://github.com/user-attachments/assets/be98734f-901f-482e-9e06-eaeb477425ac" width="800" />

### I unziped the back-end resources using the CLI

<img src="https://github.com/user-attachments/assets/e8c4f892-c21f-4fd0-9b6a-70dca5420f98" width="800" />

### I made an environment config `.env` to communicate with different services(`Bedrock`, `AWS` and `OpenAI`):

```bash
PORT=5000
AWS_REGION=us-west-2
BEDROCK_AGENT_ID=<your-bedrock-agent-id>
BEDROCK_AGENT_ALIAS_ID=<your-bedrock-agent-alias-id>
OPENAI_API_KEY=<your-openai-api-key>
OPENAI_ASSISTANT_ID=<your-openai-assistant-id>
```

<img src="https://github.com/user-attachments/assets/d47c7ce0-2224-437a-9ba8-b4535c0fbaaf" width="800" />

### I then made a `Dockerfile` to set up a containerized environment for a `Node.js` based Docker application, installs dependencies, and specifies how to run the application:

```bash
FROM node:18
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 5000
CMD ["npm", "start"]
```

### Next I made a new directory for the front-end and download the front-end resources

<img src="https://github.com/user-attachments/assets/7174eb15-83e6-4ee3-ac9a-7ddbc010c42a" width="800" />

### Then I unziped the front-end resources

<img src="https://github.com/user-attachments/assets/bb62e470-e3be-459a-9e8e-33a7b1aa5cc9" width="800" />

### Next I create another `Dockerfile` to add front-end settings for the Node.js Docker application

```bash
FROM node:16-alpine
WORKDIR /app
RUN npm install -g serve
COPY --from=build /app/dist /app
ENV PORT=5001
ENV NODE_ENV=production
EXPOSE 5001
CMD ["serve", "-s", ".", "-l", "5001"]
```

<img src="https://github.com/user-attachments/assets/d5ae877d-0ca1-4552-a3dc-b1ad39589e49" width="500" />

[Back to the top](#table-of-contents)
---

## Kubernetes Setup

### I created a user named `eksuser` in IAM with administrative privileges

<img src="https://github.com/user-attachments/assets/54000981-936e-4fe5-89fe-7d697aab1a87" width="500" />

### Next I autheniticated the `eksuser` admin:

```
Created access key in IAM:
Clicked Users> Clicked "eksuser" > Clicked "Create Access Key"
Selected CLI > Checked the confirmation box > Clicked Next
Clicked Create access key


I copied the `access key` from the dashcreen by clicking the copy button.
```

<img src="https://github.com/user-attachments/assets/56bf8f56-1d58-4b33-8bbd-cee23c75d019" width="300" />

```

I ran the command `aws configure` in CLI to enter access key
```

<img src="https://github.com/user-attachments/assets/51b2c0e3-9d9f-4253-a49d-186d76a2d69c" width="500" />

```
I then copied the secret access key and pasted it into the CLI
```

<img src="https://github.com/user-attachments/assets/3c83efe5-34bb-4020-8688-48dc4673ebbc" width="500" />

```
Entered the region: us-west-2
Kept the default output format by clicking enter.
```
[Back to the top](#table-of-contents)
---

## EKS Setup

### First I installed `eksctl` through the ES2 CLI
<img src="https://github.com/user-attachments/assets/4fc9a275-10ed-4766-a72e-c2a2d4f9b669" width="800" />

### Next I installed `kubectl` through the ES2 CLI
<img src="https://github.com/user-attachments/assets/faf75f8b-a289-4970-8b8d-657a2744faa0" width="800" />

### Then I created an EKS Cluster via the ES2 CLI:

```
eksctl create cluster \
  --name cloudmart \
  --region us-west-2 \
  --nodegroup-name standard-workers \
  --node-type t3.medium \
  --nodes 1 \
  --with-oidc \
  --managed
```

<img src="https://github.com/user-attachments/assets/5a8b46b6-a575-4512-b73c-89b12fc47d41" width="800" />

### I connected to the EKS cluster using kubectl configuration and then verified cluster connectivity
<img src="https://github.com/user-attachments/assets/8dbbfdcd-4059-438a-9c6f-6a4add7bf152" width="800" />

### I created a role and a service account to provide pods access to services used by the application (`DynamoDB`, `Bedrock`, etc)
<img src="https://github.com/user-attachments/assets/53cbffa4-b08f-42d6-9a0e-db1d57cfac07" width="800" />

---

>**NOTE:** I gave the service account admin privileges for simplicity, however, in a real-world application you would follow the "Least Privilege/Zero Trust" model.

---
[Back to the top](#table-of-contents)
---
## ECR Setup

### I created an ECR Repository for the backened to "dock" the Docker Image
<img src="https://github.com/user-attachments/assets/45004723-f568-4b18-a8fd-21eafe11d34a" width="800" />

```
 I named the repository and left all other options as default > Clicked create.
```

---

>**NOTE:** I am using a public repository for the lab, however, in the real-world you would use a private repository so you don't risk exposing your docker image to the public.

---

### Next I changed to the back-end directory on the CLI

```bash
cd ..
cd backend
```

### I followed the steps for ECR onboarding:

<img src="https://github.com/user-attachments/assets/80cdb5f4-812c-49ee-902d-299043ee4aa2" width="800" />
<img src="https://github.com/user-attachments/assets/9e7c6485-77d1-4cd6-a51b-6754e5d8c465" width="800" />
<img src="https://github.com/user-attachments/assets/fb9d651d-82b9-4c2b-bb43-9222c2410595" width="800" />

### I created a Kubernetes deployment file for the back-end

`nano cloudmart-backend.yaml`

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cloudmart-backend-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cloudmart-backend-app
  template:
    metadata:
      labels:
        app: cloudmart-backend-app
    spec:
      serviceAccountName: cloudmart-pod-execution-role
      containers:
      - name: cloudmart-backend-app
        image: public.ecr.aws/f8g6e7z8/cloudmart-backend:latest
        env:
        - name: PORT
          value: "5000"
        - name: AWS_REGION
          value: "us-west-2"
        - name: BEDROCK_AGENT_ID
          value: "xxxxxx"
        - name: BEDROCK_AGENT_ALIAS_ID
          value: "xxxx"
        - name: OPENAI_API_KEY
          value: "xxxxxx"
        - name: OPENAI_ASSISTANT_ID
          value: "xxxx"
---

apiVersion: v1
kind: Service
metadata:
  name: cloudmart-backend-app-service
spec:
  type: LoadBalancer
  selector:
    app: cloudmart-backend-app
  ports:
    - protocol: TCP
      port: 5000
      targetPort: 5000
```
      
---

>**NOTE:** I changed the "image:" line to the ":latest" URI Image from the ECR instance I created. This can be found by clicking on the back-end registry in ECR and clicking "copy URI". 

---

### I appled the `.yaml` configure file using the CLI tool

```bash
kubectl apply -f cloudmart-backend.yaml
```

<img src="https://github.com/user-attachments/assets/78e9f2d4-2621-43cf-8601-f66560b7afa5" width="800" />

### I monitored the status of objects being created and obtained the public IP generated for the API
<img src="https://github.com/user-attachments/assets/88c5ff87-814b-43b0-8dcc-0d1c61f90d08" width="800" />

### I changed to the front-end directory to work on the front-end:

```
cd ..
cd frontend
```

### I then used the CLI to get the URL for the Kubernetes API
<img src="https://github.com/user-attachments/assets/6dd00d33-5a80-4470-91c5-afd396f6af1e" width="800" />

### Next I copied the URL to appened the following:

```
nano .env
```

```
VITE_API_BASE_URL=http://<your_url_kubernetes_api>:5000/api
```

### After that I created an ECR Repository for the front-end
<img src="https://github.com/user-attachments/assets/a3fa488d-6120-4c88-8317-b385d6c107c6" width="800" />

```
I named the repository and left all other options as default > Clicked create.
```

### I followed the steps for ECR onboarding like I did for the back-end
<img src="https://github.com/user-attachments/assets/40ff1f55-ea25-40f6-9171-89f1b316c375" width="800" />


### I created a Kubernetes deployment file for the front-end:

```
nano cloudmart-frontend.yaml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cloudmart-frontend-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cloudmart-frontend-app
  template:
    metadata:
      labels:
        app: cloudmart-frontend-app
    spec:
      serviceAccountName: cloudmart-pod-execution-role
      containers:
      - name: cloudmart-frontend-app
        image: public.ecr.aws/f8g6e7z8/cloudmart-frontend:latest
---

apiVersion: v1
kind: Service
metadata:
  name: cloudmart-frontend-app-service
spec:
  type: LoadBalancer
  selector:
    app: cloudmart-frontend-app
  ports:
    - protocol: TCP
      port: 5001
      targetPort: 5001
```

---

>**NOTE:** I changed the "image:" line to the ":latest" URI Image from the ECR instance I created. This can be found by clicking on the front-end regisitry in ECR and clicking "copy URI".

---

### I applied the deployment file through the CLI tool
<img src="https://github.com/user-attachments/assets/16b69edf-6197-4484-beda-6e491773fa6c" width="800" />

### I used the CLI to verify the services are running
<img src="https://github.com/user-attachments/assets/d162aac9-c885-4304-84ca-b7ecc14eea29" width="800" />

[Back to the top](#table-of-contents)
---

## STAGE 2 - Wrap-Up

### I decided to check the Web facing API to see what I have so far
<img src="https://github.com/user-attachments/assets/a2714e7f-2b52-43af-ac44-2297a308ceb2" width="800" />

### Here I have a working API that I can add products to
<img src="https://github.com/user-attachments/assets/6e0c72df-f7e3-422c-b627-8696f624b7ec" width="800" />
<img src="https://github.com/user-attachments/assets/ce839899-bbb4-4d03-bb95-d87ef736accb" width="800" />

[Back to the top](#table-of-contents)
---

# STAGE 3 - Pipeline (Automation) Setup

## GitHub Setup 

### I set up a GitHub repository so I can use it for a CI/CD pipeline to the front-end

<img src="https://github.com/user-attachments/assets/aa85b7ca-de78-4353-b552-d5e544af5c36" width="500">

### I named the repository "cloudmart", then in the EC2 CLI:

```
echo "# cloudmart" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/Goodka7/cloudmart.git
git push -u origin main
```

### I generated a password token on GitHub for the `origin main` step of the script above

<img src="https://github.com/user-attachments/assets/6ae9dfe9-a235-46e9-8fe8-29104c350a8b" width="300">

### I used the token in the password field
<img src="https://github.com/user-attachments/assets/f9e7ad8a-f98c-49ab-9501-8a44579a9f4a" width="800">

### I used `git add` and `git commit` to create the files for the repository

```
git add -A
git commit -m "app sent to repo"
```

<img src="https://github.com/user-attachments/assets/4ab3478b-7e7c-474c-9fbf-fc3f11548c5d" width="800">

### Next I used `git push` to push it through in to the repository
<img src="https://github.com/user-attachments/assets/856ef0a9-ee39-43cf-ab33-a7c035b64b62" width="800">

### Finally I manually checked the repository to see if the push went through
<img src="https://github.com/user-attachments/assets/6ac42db8-6fe1-42b7-a1fc-3204e6505e8b" width="800">

[Back to the top](#table-of-contents)
---

## CodePipeline Setup

### Access AWS CodePipeline

### Start the 'Create pipeline' process:

```
Name: `cloudmart-cicd-pipeline`
Use the GitHub repository `cloudmart` as the source
```

<img src="https://github.com/user-attachments/assets/1aaaca58-98bb-4fdf-80f0-750c92373937" width="500">

---

>**NOTE:** Normally I would choose the GitHub (GitHub App) authetication, but because this is a lab I am going to use OAUTH for simplicity.

---

```
Use the GitHub Branch `main` as the source

Set build provider to "Other build providers" and select `AWSCodeBuild`
Make sure `SourceArtifact` is set for Input Artifact
```

<img src="https://github.com/user-attachments/assets/93dc4d91-02c1-48f1-b083-8b0b8b8ec3c4" width="500">

```
Click "Create Project"
Give the project the name `cloudmartBuild`.
Select `amazonlinux-x86_64-standard:4.0` as the Image.
```

<img src="https://github.com/user-attachments/assets/588c0023-4ad2-454d-9fe6-5b046e5208f2" width="500">

```
Configure the environment to support Docker builds by checking "Enable this flag if you want to build Docker images or want your builds to get elevated privileges"
```

<img src="https://github.com/user-attachments/assets/5e015e61-dc48-4e0c-be58-860a490f528a" width="500">

```
Add the environment variable **ECR_REPO** with the ECR front-end repository URI.
```

---

>**NOTE:** Do NOT use :latest as this will give you an error when running the "build" process.

---

<img src="https://github.com/user-attachments/assets/0bb21ca3-2033-400c-a431-b5f2ecf7d65b" width="500">

### I used the following for the build specification:

```
version: 0.2
phases:
  install:
    runtime-versions:
      docker: 20
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws --version
      - REPOSITORY_URI=$ECR_REPO
      - aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/f8g6e7z8
  build:
    commands:
      - echo Build started on `date`
      - echo Building the Docker image...
      - docker build -t $REPOSITORY_URI:latest .
      - docker tag $REPOSITORY_URI:latest $REPOSITORY_URI:$CODEBUILD_RESOLVED_SOURCE_VERSION
  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing the Docker image...
      - docker push $REPOSITORY_URI:latest
      - docker push $REPOSITORY_URI:$CODEBUILD_RESOLVED_SOURCE_VERSION
      - export imageTag=$CODEBUILD_RESOLVED_SOURCE_VERSION
      - printf '[{\"name\":\"cloudmart-app\",\"imageUri\":\"%s\"}]' $REPOSITORY_URI:$imageTag > imagedefinitions.json
      - cat imagedefinitions.json
      - ls -l

env:
  exported-variables: ["imageTag"]

artifacts:
  files:
    - imagedefinitions.json
    - cloudmart-frontend.yaml
```

---

>**NOTE:** The "project build creator" will ask you if you're okay to "Leave" the site, and suggests your options won't be saved, click "Leave".

---

<img src="https://github.com/user-attachments/assets/02e20878-63c9-436d-b418-20cce7416b2a" width="500">

```
Click "Create Pipeline"
Click "Add permissions"
Set permission `AmazonElasticContainerRegistryPublicFullAccess` for role created by the pipeline process, in IAM.

```

<img src="https://github.com/user-attachments/assets/f09789d2-60d1-48af-98cd-5f8213fed46c" width="500">

```
Continue through by skipping the next few screens with "Next", it will be the Test and Deploy screens.
The pipeline will execute the build.
```

<img src="https://github.com/user-attachments/assets/865dd6d4-0e26-4361-8444-fc311e75ac49" width="500">

---

>**NOTE:** The "build" portion will fail if you do not set up the permission in the role before running, if so, just run again after assigning the permission.

---

### Next I set up the deployment: 

```
Click "Edit" on the pipeline page
After "build" click "+Add Stage"
```

<img src="https://github.com/user-attachments/assets/2bf9a7bd-9d39-4aa7-83be-a71fac81976d" width="500">
<img src="https://github.com/user-attachments/assets/eea203f2-9ac1-44a8-9720-ebfbb51f3aea" width="500">

```
Click "add action group".
```
<img src="https://github.com/user-attachments/assets/97523f5e-2ab3-49e2-93d4-77bdb5307497" width="500">

```
Once all this information is entered, click "Create project"
Title the project: `cloudmartDeployToProduction`
Scroll down to image and make sure `amazonlinux-x86_64-standard:4.0` is selected.
Created two Enviromental Variables: `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` 
```

### I entered the access key values for the `eksuser` that I created earlier

---

>**NOTE:** These two variables will be used to authenticate to Kubernetes. You can create new keys if you lost them.

---

<img src="https://github.com/user-attachments/assets/53deaeb5-2bd8-462d-bd1f-a5e8dfee8393" width="500">

---

>**NOTE:** Normally you would not manually do this in a production environment as it doesn't follow security best practices.

---

```
Scroll down to build spec and click "switch to Editor":
```

### I entered the following script:

```
version: 0.2

phases:
  install:
    runtime-versions:
      docker: 20
    commands:
      - curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.18.9/2020-11-02/bin/linux/amd64/kubectl
      - chmod +x ./kubectl
      - mv ./kubectl /usr/local/bin
      - kubectl version --short --client
  post_build:
    commands:
      - aws eks update-kubeconfig --region us-west-2 --name cloudmart
      - kubectl get nodes
      - ls
      - IMAGE_URI=$(jq -r '.[0].imageUri' imagedefinitions.json)
      - echo $IMAGE_URI
      - sed -i "s|CONTAINER_IMAGE|$IMAGE_URI|g" cloudmart-frontend.yaml
      - kubectl apply -f cloudmart-frontend.yaml
```

---

>**NOTE:** This build script allows for the kubernetes to search the imagedefinitions.json file to find the current imageUri, then then change the value of the Uri variable inside cloudmart-frontend.yaml

---

### Next, I changed the value hard coded value in the `.yaml` and placed a variable:

```
nano cloudmart-frontend.yaml
```

<img src="https://github.com/user-attachments/assets/3ee566f1-28fe-4e97-a438-69b4a3446ab1" width="500">

### After that I commit and push changes:

```
git add -A
git commit -m "replaced image uri with CONTAINER_IMAGE"
git push
```

<img src="https://github.com/user-attachments/assets/fb1b3711-bba5-424c-9b7f-c3865c2a8009" width="800">

[Back to the top](#table-of-contents)
---
## STAGE 3 - Wrap-Up

### In the CLI, using nano, I append the file `src/components/MainPage/index.jsx` line 93 to say "Featured Products on Cloudmart" 

<img src="https://github.com/user-attachments/assets/ea5c6a6b-2c7c-445f-b799-c33c1812f905" width="500">
<img src="https://github.com/user-attachments/assets/c7573990-708a-4c31-b734-c6f33e26f6bd" width="500">

### Before:
<img src="https://github.com/user-attachments/assets/6932516a-1e2b-49cb-9f65-2e65d624de27" width="800">

### Now I commit and push the changes:

```
  git add -A
  git commit -m "changed to Featured Products on CloudMart"
  git push
```

<img src="https://github.com/user-attachments/assets/c66256f6-3a55-459c-a7f8-123f744e4ad6" width="500">

### After:
<img src="https://github.com/user-attachments/assets/978b2dfb-fa4e-4d17-bc94-1da504fd0fc7" width="800">

[Back to the top](#table-of-contents)
---

# STAGE 4 - AI Assistant Setup

## Lambda Setup

### First I copied the `products.zip` into the `terraform-project` folder

```
cd challenge-day2/backend/src/lambda
cp list_products.zip ../../../../terraform-project/
cd ../../../../terraform-project
```

### Next I appended the `main.tf` to add:

```
# IAM Role for Lambda function
resource "aws_iam_role" "lambda_role" {
  name = "cloudmart_lambda_role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "lambda.amazonaws.com"
        }
      }
    ]
  })
}

# IAM Policy for Lambda function
resource "aws_iam_role_policy" "lambda_policy" {
  name = "cloudmart_lambda_policy"
  role = aws_iam_role.lambda_role.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "dynamodb:Scan",
          "logs:CreateLogGroup",
          "logs:CreateLogStream",
          "logs:PutLogEvents"
        ]
        Resource = [
          aws_dynamodb_table.cloudmart_products.arn,
          aws_dynamodb_table.cloudmart_orders.arn,
          aws_dynamodb_table.cloudmart_tickets.arn,
          "arn:aws:logs:*:*:*"
        ]
      }
    ]
  })
}

# Lambda function for listing products
resource "aws_lambda_function" "list_products" {
  filename         = "list_products.zip"
  function_name    = "cloudmart-list-products"
  role             = aws_iam_role.lambda_role.arn
  handler          = "index.handler"
  runtime          = "nodejs20.x"
  source_code_hash = filebase64sha256("list_products.zip")

  environment {
    variables = {
      PRODUCTS_TABLE = aws_dynamodb_table.cloudmart_products.name
    }
  }
}

# Lambda permission for Bedrock
resource "aws_lambda_permission" "allow_bedrock" {
  statement_id  = "AllowBedrockInvoke"
  action        = "lambda:InvokeFunction"
  function_name = aws_lambda_function.list_products.function_name
  principal     = "bedrock.amazonaws.com"
}

# Output the ARN of the Lambda function
output "list_products_function_arn" {
  value = aws_lambda_function.list_products.arn
}
```

---

>**NOTE:** This script creates an IAM role and policy for a Lambda function, the Lambda function itself (which lists products from DynamoDB), permissions for Bedrock to invoke the function, and outputs the ARN of the Lambda function.

---

### I used `terraform apply` in order to push changes

<img src="https://github.com/user-attachments/assets/743c2081-d185-47fb-affc-68557a25fff9" width="500">

[Back to the top](#table-of-contents)
---
## Bedrock Agent Setup

---

>**NOTE:** I will be using the Claude 3 Sonnet verison even though its considered a `legacy`, this is because the set up is very easy.

---

### I requested the agent, by searching the list for the agent `Claude 3 Sonnet`

<img src="https://github.com/user-attachments/assets/7a7ef9fa-b296-45dc-9bbf-6baefaa04bf0" width="500">

### Under "Builder Tools" on the left side of the screen, I selected "Agent":

```
Click "Create Agent".
```
<img src="https://github.com/user-attachments/assets/f0205cd9-192c-46d4-af26-dde9a1fffe38" width="800">

### I inserted the following for Agent Instructions:

```
You are a product recommendations agent for CloudMart, an online e-commerce store. Your role is to assist customers in finding products that best suit their needs. Follow these instructions carefully:

1. Begin each interaction by retrieving the full list of products from the API. This will inform you of the available products and their details.

2. Your goal is to help users find suitable products based on their requirements. Ask questions to understand their needs and preferences if they're not clear from the user's initial input.

3. Use the 'name' parameter to filter products when appropriate. Do not use or mention any other filter parameters that are not part of the API.

4. Always base your product suggestions solely on the information returned by the API. Never recommend or mention products that are not in the API response.

5. When suggesting products, provide the name, description, and price as returned by the API. Do not invent or modify any product details.

6. If the user's request doesn't match any available products, politely inform them that we don't currently have such products and offer alternatives from the available list.

7. Be conversational and friendly, but focus on helping the user find suitable products efficiently.

8. Do not mention the API, database, or any technical aspects of how you retrieve the information. Present yourself as a knowledgeable sales assistant.

9. If you're unsure about a product's availability or details, always check with the API rather than making assumptions.

10. If the user asks about product features or comparisons, use only the information provided in the product descriptions from the API.

11. Be prepared to assist with a wide range of product inquiries, as our e-commerce store may carry various types of items.

12. If a user is looking for a specific type of product, use the 'name' parameter to search for relevant items, but be aware that this may not capture all categories or types of products.

Remember, your primary goal is to help users find the best products for their needs from what's available in our store. Be helpful, informative, and always base your recommendations on the actual product data provided by the API.
```
```
Scroll up and click "Save and Exit"
Scroll down on the cloudmart-product-recommendation-agent overview
```

<img src="https://github.com/user-attachments/assets/94947079-53f3-4428-b560-67a2ff5559fc" width="300">

```
Click the link to open in a NEW TAB.
Click "Add permissions".
Select "Create inline policy".
```

---

>**NOTE:**This step adds a permission so the "agent" can use the Lambda function.

---

### I selected "json" at the top and add the following script:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "lambda:InvokeFunction",
      "Resource": "arn:aws:lambda:*:*:function:cloudmart-list-products"
    },
    {
      "Effect": "Allow",
      "Action": "bedrock:InvokeModel",
      "Resource": "arn:aws:bedrock:*::foundation-model/anthropic.claude-3-sonnet-20240229-v1:0"
    }
  ]
}
```
```
Click "Next".
Name the policy `BedrockAgentLambdaAccess`.
Click "Create policy".
```

<img src="https://github.com/user-attachments/assets/6f07ef5d-dfe4-4e56-b67a-2c5971a310a1" width="800">

### Next I added an Action Group:

```
Going back to the Amazon Bedrock screen, click `Edit in Agent Builder`.
Scroll down to the Action Group section and click `Add`.
Set the Action Group name to `Get-Product-Recommendations`.
Set the action group type as `Define with API schemas`.
Select the Lambda function `cloudmart-list-products` as the action group executor.
In the `Action group schema` section, choose `Define via in-line schema editor`.
```

### I paste the OpenAPI schema below into the schema editor:

```
{
    "openapi": "3.0.0",
    "info": {
        "title": "Product Details API",
        "version": "1.0.0",
        "description": "This API retrieves product information. Filtering parameters are passed as query strings. If query strings are empty, it performs a full scan and retrieves the full product list."
    },
    "paths": {
        "/products": {
            "get": {
                "summary": "Retrieve product details",
                "description": "Retrieves a list of products based on the provided query string parameters. If no parameters are provided, it returns the full list of products.",
                "parameters": [
                    {
                        "name": "name",
                        "in": "query",
                        "description": "Retrieve details for a specific product by name",
                        "schema": {
                            "type": "string"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "Successful response",
                        "content": {
                            "application/json": {
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "type": "object",
                                        "properties": {
                                            "name": {
                                                "type": "string"
                                            },
                                            "description": {
                                                "type": "string"
                                            },
                                            "price": {
                                                "type": "number"
                                            }
                                        }
                                    }
                                }
                            }
                        }
                    },
                    "500": {
                        "description": "Internal Server Error",
                        "content": {
                            "application/json": {
                                "schema": {
                                    "$ref": "#/components/schemas/ErrorResponse"
                                }
                            }
                        }
                    }
                }
            }
        }
    },
    "components": {
        "schemas": {
            "ErrorResponse": {
                "type": "object",
                "properties": {
                    "error": {
                        "type": "string",
                        "description": "Error message"
                    }
                },
                "required": [
                    "error"
                ]
            }
        }
    }
}
```

```
Click on `Prepare Agent` to finalize the creation.
```

### I ran a small test on the agent on the right
<img src="https://github.com/user-attachments/assets/b56a9082-ba28-48c6-8e62-1b593cdf4edf" width="300">

### I then created an alias for the agent:

```
On the agent details page, go to the `Aliases` section.
Click on `Create alias`.
Name the alias `cloudmart-prod`.
Select the most recent version of the agent.
Click on `Create alias` to finalize.
```
[Back to the top](#table-of-contents)
---
## OpenAI Agent Setup

```
Access the OpenAI platform (https://platform.openai.com/).
Log in.
Navigate to the "Assistants" section.
Click on "Create New Assistant".
Name the assistant "CloudMart Customer Support".
Select the model `gpt-4o`.
```

### I pasted the following in the "Instructions" section:

```
You are a customer support agent for CloudMart, an e-commerce platform. Your role is to assist customers with general inquiries, order issues, and provide helpful information about using the CloudMart platform. You don't have direct access to specific product or inventory information. Always be polite, patient, and focus on providing excellent customer service. If a customer asks about specific products or inventory, politely explain that you don't have access to that information and suggest they check the website or speak with a sales representative.
```

```
Enable "Code Interpreter" so that the assistant is able to help with technical aspects of using the platform.
Note down the Assistant ID
Go to the API Keys section in your OpenAI account.
Generate a new API key.
Copy this key, for the environment variables.
```

### I switched back to the EC2 instance and changed directories:

```
cd ../..
cd challenge-day2/backend
```

### Next I appended the `cloudmart-backend.yaml` file to allow our AI access:
```
nano cloudmart-backend.yaml
```
```
        - name: BEDROCK_AGENT_ID
          value: "xxxx"
        - name: BEDROCK_AGENT_ALIAS_ID
          value: "xxxx"
        - name: OPENAI_API_KEY
          value: "xxxx"
        - name: OPENAI_ASSISTANT_ID
          value: "xxxx"
```          
<img src="https://github.com/user-attachments/assets/5f8afd54-14f9-4cfa-9b9d-8d5f7d2c6c37" width="500">

---

>**NOTE:** This info is on the OpenAI assistant page as well as the Bedrock agent page.

---

### Then I pushed the appended `.yaml` into the environment

<img src="https://github.com/user-attachments/assets/9b7f0054-803c-4e81-94d5-c2178de5df35" width="500">

[Back to the top](#table-of-contents)
---
## Stage 4 - Wrap-Up

### I tested the agents in the Web Facing API

### Bedrock AI Agent:
<img src="https://github.com/user-attachments/assets/6830d70e-cab9-436f-8a40-a5e5c49973bf" width="300">

### Open AI Assistant:
<img src="https://github.com/user-attachments/assets/0899df3b-569c-44c9-9f3b-397abb3e65f1" width="300">

Here we have two functioning AI bots that are capable of handling customer queries and even cancel orders:

<img src="https://github.com/user-attachments/assets/7db8b10a-891d-424a-a87d-299c57f69f83" width="600">
<img src="https://github.com/user-attachments/assets/829fa8b3-20bf-4b2f-b0ed-90e5b83b5297" width="600">
<img src="https://github.com/user-attachments/assets/008347bc-2576-4dc9-9bd9-a7f5d704c762" width="600">
<img src="https://github.com/user-attachments/assets/74958021-b027-4808-af82-aa7a418235d9" width="600">

[Back to the top](#table-of-contents)
---

# STAGE 5 - MultiCloud Setup

---

>**NOTE:** The next few steps are a quick way to create a backup and clear the folders out to move forward, in the real-world I would do this much differently.

---


### I backed up the existing folders:

```
cp -R challenge-day2/ challenge-day2_bkp
cp -R terraform-project/ terraform-project_bkp
```

### I cleaned up the existing application files except the `Dockerfile` and the `.yaml` file (back-end):

```
cd challenge-day2/backend
rm -rf $(find . -mindepth 1 -maxdepth 1 -not \( -name ".*" -o -name Dockerfile -o -name "*.yaml" \))
```
<img src="https://github.com/user-attachments/assets/b9183530-22da-48db-8b5f-6413f2e7c523" width="300">

### I downloaded the updated source code and unziped it (back-end):

```
wget  https://tcb-public-events.s3.amazonaws.com/mdac/resources/final/cloudmart-backend-final.zip
unzip cloudmart-backend-final.zip
```
<img src="https://github.com/user-attachments/assets/07142000-a1f3-4cf4-9856-0211d14433fc" width="500">

### I cleaned up the existing application files except the `Dockerfile` and the `.yaml` file (front-end):

```
cd ../..
cd challenge-day2/frontend
rm -rf $(find . -mindepth 1 -maxdepth 1 -not \( -name ".*" -o -name Dockerfile -o -name "*.yaml" \))
```
<img src="https://github.com/user-attachments/assets/cc9e2359-723e-44bb-a6db-1f5218369634" width="300">

### I downloaded the updated source code and unziped it (front-end):

```
wget  https://tcb-public-events.s3.amazonaws.com/mdac/resources/final/cloudmart-frontend-final.zip
unzip cloudmart-frontend-final.zip
```
<img src="https://github.com/user-attachments/assets/11cc3198-9a22-42b8-9e95-e76df134b216" width="800">

### I used the CI/CD pipeline to push the updates to the front-end:

```
git add -A
git commit -m "final code"
git push 
```
<img src="https://github.com/user-attachments/assets/4f85a39a-1916-400a-bbce-c228bdcf0ac2" width="800">

[Back to the top](#table-of-contents)
---

## BigQuery Setup

### I followed these steps to set up Google Cloud BigQuery for CloudMart:

```
Create a Google Cloud Project:
Go to the Google Cloud Console (https://console.cloud.google.com/).
Click on the project dropdown and select "New Project".
Name the project "CloudMart" and click "create".
```
```
Enable BigQuery API:
In the Google Cloud Console (the hamburger menu on the top left of the dashboard).
Click "APIS & SERVICES" to open the dashboard.
Click "+ ENABLE APIS AND SERVICES" at the top.
Search for "BigQuery API" and enable it.
```
```
Create a BigQuery Dataset:
In the Google Cloud Console, go to "BigQuery".
In the Explorer pane, click on your project name.
Click the menu on the right.
Click "Create dataset".
Set the Dataset ID to "cloudmart".
Click "Create dataset".
```
```
Create a BigQuery Table:
In the dataset you just created, click the menu on the right.
Click "Create table".
Set the Table name to "cloudmart-orders".
Define the schema according to your order structure. For example:
  - id: STRING
  - items: JSON
  - userEmail: STRING
  - total: FLOAT
  - status: STRING
  - createdAt: TIMESTAMP
Click "Create table".
```
```
Create Service Account and Key:
In the Google Cloud Console, go to "IAM & Admin" > "Service Accounts".
Click "Create service account".
Name it "cloudmart-bigquery-sa" and click "Create and Continue"
Grant it the "BigQuery Data Editor" role.
After creating, click on the service account, go to the "Keys" tab, and click "ADD KEY" > "Create new key".
Choose JSON as the key type and create.
Save the downloaded JSON file as `google_credentials.json`.
```
[Back to the top](#table-of-contents)
---

## Configure Lambda Function

### I navigated to the root directory of my project.
```
cd ../..
```

### Next I entered the Lambda function directory:
 ```       
 cd challenge-day2/backend/src/lambda/addToBigQuery
 ```       
### After that I installed the required dependencies:
```        
sudo yum install npm
npm install
```
                
### Then I edited the `google_credentials.json` file in this directory and placed the content of my key.

---

>**NOTE:** I copy/paste for simplicity's sake, but in the real-world this would not but the best practice, there are many other ways to do this.

---
   
### I created a zip file of the entire directory:
```        
zip -r dynamodb_to_bigquery.zip .
```

---

>**NOTE:** This zip file will be used when creating or updating the Lambda function.

---

### I returned to the root directory of the project:
``` 
cd ../..
```

---

>**WARNING:** In the real world never commit the `google_credentials.json` file to version control. It should be added to your `.gitignore` file.

---

### Next I updated Lambda Function Environmental Variables

## Terraform Steps

### First I deleted the `main.tf`

```
cd terraform-project
rm main.tf
```

### Next I recreated the file using `nano`

```
nano main.tf

```

### Then I put the following script inside the `main.tf`

```
provider "aws" {
  region = "us-east-1" 
}

# Tables DynamoDB
resource "aws_dynamodb_table" "cloudmart_products" {
  name           = "cloudmart-products"
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "id"

  attribute {
    name = "id"
    type = "S"
  }
}

resource "aws_dynamodb_table" "cloudmart_orders" {
  name           = "cloudmart-orders"
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "id"

  attribute {
    name = "id"
    type = "S"
  }
  
  stream_enabled   = true
  stream_view_type = "NEW_AND_OLD_IMAGES"
}

resource "aws_dynamodb_table" "cloudmart_tickets" {
  name           = "cloudmart-tickets"
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "id"

  attribute {
    name = "id"
    type = "S"
  }
}

# IAM Role for Lambda function
resource "aws_iam_role" "lambda_role" {
  name = "cloudmart_lambda_role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "lambda.amazonaws.com"
        }
      }
    ]
  })
}

# IAM Policy for Lambda function
resource "aws_iam_role_policy" "lambda_policy" {
  name = "cloudmart_lambda_policy"
  role = aws_iam_role.lambda_role.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "dynamodb:Scan",
          "dynamodb:GetRecords",
          "dynamodb:GetShardIterator",
          "dynamodb:DescribeStream",
          "dynamodb:ListStreams",
          "logs:CreateLogGroup",
          "logs:CreateLogStream",
          "logs:PutLogEvents"
        ]
        Resource = [
          aws_dynamodb_table.cloudmart_products.arn,
          aws_dynamodb_table.cloudmart_orders.arn,
          "${aws_dynamodb_table.cloudmart_orders.arn}/stream/*",
          aws_dynamodb_table.cloudmart_tickets.arn,
          "arn:aws:logs:*:*:*"
        ]
      }
    ]
  })
}

# Lambda function for listing products
resource "aws_lambda_function" "list_products" {
  filename         = "list_products.zip"
  function_name    = "cloudmart-list-products"
  role             = aws_iam_role.lambda_role.arn
  handler          = "index.handler"
  runtime          = "nodejs20.x"
  source_code_hash = filebase64sha256("list_products.zip")

  environment {
    variables = {
      PRODUCTS_TABLE = aws_dynamodb_table.cloudmart_products.name
    }
  }
}

# Lambda permission for Bedrock
resource "aws_lambda_permission" "allow_bedrock" {
  statement_id  = "AllowBedrockInvoke"
  action        = "lambda:InvokeFunction"
  function_name = aws_lambda_function.list_products.function_name
  principal     = "bedrock.amazonaws.com"
}

# Output the ARN of the Lambda function
output "list_products_function_arn" {
  value = aws_lambda_function.list_products.arn
}

# Lambda function for DynamoDB to BigQuery
resource "aws_lambda_function" "dynamodb_to_bigquery" {
  filename         = "../challenge-day2/backend/src/lambda/addToBigQuery/dynamodb_to_bigquery.zip"
  function_name    = "cloudmart-dynamodb-to-bigquery"
  role             = aws_iam_role.lambda_role.arn
  handler          = "index.handler"
  runtime          = "nodejs20.x"
  source_code_hash = filebase64sha256("../challenge-day2/backend/src/lambda/addToBigQuery/dynamodb_to_bigquery.zip")

  environment {
    variables = {
      GOOGLE_CLOUD_PROJECT_ID        = "cloudmart-451713"
      BIGQUERY_DATASET_ID            = "cloudmart"
      BIGQUERY_TABLE_ID              = "cloudmart-orders"
      GOOGLE_APPLICATION_CREDENTIALS = "/var/task/google_credentials.json"
    }
  }
}

# Lambda event source mapping for DynamoDB stream
resource "aws_lambda_event_source_mapping" "dynamodb_stream" {
  event_source_arn  = aws_dynamodb_table.cloudmart_orders.stream_arn
  function_name     = aws_lambda_function.dynamodb_to_bigquery.arn
  starting_position = "LATEST"
}
```

### Apply the changes using `terraform apply`:

```
terraform apply
```

<img src="https://github.com/user-attachments/assets/b2f78c50-5d93-4e97-b8f6-492c153b2c5d" width="800">

[Back to the top](#table-of-contents)
---

## Azure Language Setup

### I followed these steps to set up Azure Language for sentiment analysis:

```
Create an Azure Account:
Go to the Azure portal (https://portal.azure.com/).
Sign in or create a new account if you don't have one.
```
```
Create a Resource:
In the Azure portal, click "Create a resource".
Search for "Language" and select it.
```
```
Configure the Resource:
Choose your subscription and resource group (create a new one if needed).
Name the resource (e.g., "cloudmart-text-analytics").
Choose your region and pricing tier.
Name your instance (e.g., "cloud-instance")
Click "Review + create", then "Create".
```
```
Get the Endpoint and Key:
Once the resource is created, go to its overview page.
In the left menu, under "Resource Management", click "Keys and Endpoint".
Copy the endpoint URL and one of the keys.
```

---

## Change Deployment

### I then changed directories:

```
cd ..
cd backend
```

### Next I opened the `cloudmart-backend.yaml` to append it:

```
        - name: AZURE_ENDPOINT
          value: "xxxx"
        - name: AZURE_API_KEY
          value: "xxxx"
```
<img src="https://github.com/user-attachments/assets/5aa6de49-c402-4a47-9935-afd269b793e1" width="500">

### After that I built a new image

<img src="https://github.com/user-attachments/assets/aa7f4bff-98e3-44e3-9988-03d3eb6ae35c" width="800">

---

>**NOTE:** Since I only set up the CI/CD pipeline for the front-end, I have to manually do the back-end (steps above), ideally you would set up a pipeline for both front and back-end.

---

### Finally, I used the `kubectl` tool to apply all changes.

```
kubectl apply -f cloudmart-backend.yaml
```
[Back to the top](#table-of-contents)
---
## STAGE 5 - Wrap-Up

### First I created a new order
<img src="https://github.com/user-attachments/assets/a12ae9f6-8851-48ec-b793-f00a27ee3a22" width="300">

### Next I confirmed the order is in cart
<img src="https://github.com/user-attachments/assets/ae744ae9-66c6-42f6-a8d4-9def2f662ee4" width="500">

### Then I placed the order
<img src="https://github.com/user-attachments/assets/9b84c25f-ddae-461d-ad41-6bf6ff231a6a" width="400">

### After that I viewed confirmation
<img src="https://github.com/user-attachments/assets/6cfee2a9-3417-4963-8161-7a3bb4e72589" width="500">

### I went to Google Cloud to look in BigQuery to see if the data was collected
<img src="https://github.com/user-attachments/assets/42ef2630-0bfa-4e12-9a8c-94f46c6d2b6d" width="500">

---

>**NOTE:** This confirms the lambda function and the stream are working.

---

### Next I made a conversation in the support tab that was very negative
<img src="https://github.com/user-attachments/assets/725a0d47-ec8b-43cb-ad55-74a293475a6c" width="500">

### I then viewed the `/tickets` to see if the Azure Language API was assigning a `sentiment` 
<img src="https://github.com/user-attachments/assets/3f6a513b-0587-433f-ad8c-62a4c836911e" width="500">

### I followed up with a very positive conversation to further verify the Azure Language implementation
<img src="https://github.com/user-attachments/assets/57b078ab-62e4-46e3-95d3-e91e740a97a5" width="500">

### I was able to get all 3 sentiments that the Azure Language API gives
<img src="https://github.com/user-attachments/assets/062a2012-e499-470d-8fbb-52c7e20c09c1" width="500">

[Back to the top](#table-of-contents)
---

## Lessons Learned

1. **Importance of Clear Documentation**: Documenting every step of the process, including configuration settings, command-line inputs, and troubleshooting steps, proved to be essential for future reference and troubleshooting. This helped ensure consistency and clarity across all stages of the project.

2. **Automation and Infrastructure as Code**: The use of tools like Terraform and CodePipeline for automating infrastructure deployment and CI/CD processes significantly reduced manual errors and improved efficiency. It’s clear that adopting an Infrastructure as Code (IaC) approach helps with scalability and ensures reproducibility across environments.

3. **Containerization and Kubernetes**: Docker and Kubernetes enabled efficient containerization of both front-end and back-end services, streamlining the development and deployment process. However, ensuring proper configuration of Kubernetes (such as IAM roles and service accounts) was crucial to the success of this setup.

4. **Cloud Platform Flexibility**: Leveraging multiple cloud platforms, including AWS, Google Cloud, and Azure, showcased the flexibility that different cloud services bring to a project. Each platform offered unique tools and services, such as BigQuery and Azure Language API, which enhanced the overall system’s capabilities.

5. **AI Integration**: Implementing AI tools like OpenAI and Bedrock for customer support and product recommendations demonstrated the power of machine learning models in real-world applications. Integrating these services required careful attention to API permissions and role management to ensure smooth functionality.

6. **Security Best Practices**: Proper configuration of IAM roles and policies was critical in securing the infrastructure and ensuring that the right permissions were granted to the right services. The principle of "Least Privilege" was reinforced as essential to minimize security risks.

7. **Continuous Improvement of Deployment Pipelines**: The creation and refinement of a CI/CD pipeline using GitHub Actions and CodePipeline highlighted the importance of automating the build, test, and deployment process. This not only improved deployment speed but also ensured that updates were consistent and reliable.

8. **Scalability Considerations**: Building with scalability in mind—particularly through the use of cloud resources like DynamoDB, Kubernetes, and ECR—ensured that the system could handle future growth without significant rework.

9. **Markdown Practice**: Writing the report provided an opportunity to practice Markdown, improving both my technical writing and formatting skills.

10. **Problem Solving**: The source material I was using to create this lab was outdated, with some services no longer available and drastic changes to GUIs. I had to leverage Google and ChatGPT to find workarounds for these issues. Despite these challenges, I was able to build a working product that functioned exactly as the source material intended.

[Back to the top](#table-of-contents)
---
