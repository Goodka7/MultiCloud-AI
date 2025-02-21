##AWS Virtual Machine Set Up

Provision a VM with EC2
![image](https://github.com/user-attachments/assets/f6f8e75c-e9a4-46cd-b480-c0cac552cb6c)

Launch EC2 Instance
![image](https://github.com/user-attachments/assets/1c208424-1f25-4ebf-ae0b-770db497a741)

##Terraform Set Up

Install Terraform.
![image](https://github.com/user-attachments/assets/1907d4ad-3a76-4100-88b4-5b7cd224255d)

Use nano to create a config for S3Bucket:

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

Initialize Terraform.
![image](https://github.com/user-attachments/assets/aabee7cc-d647-4064-a3ad-3e7d78b10ec0)

Use Terraform plan to show what our config file will be doing.
![image](https://github.com/user-attachments/assets/9e9dca6f-6162-4923-9a35-9ce30f66dddd)

Apply via Terraform to automate the S3 bucket creation.
![image](https://github.com/user-attachments/assets/564daa6a-4377-4b7d-b945-74ea189c95e6)

Veryify in s3 that the bucket was created.
![image](https://github.com/user-attachments/assets/7ba5e91f-c59a-4bfb-afe5-1cfc37e73bdd)

Edit the main.tf file to create tables.
![image](https://github.com/user-attachments/assets/f4697f46-cbcf-4b3b-86d3-5fce97c07e4a)

Apply Terraform to automate the table creation and remove the S3 bucket.
![image](https://github.com/user-attachments/assets/1fa6b58c-7a64-412a-a469-f6f5da42939d)

##Docker set up.

Install Docker.
![image](https://github.com/user-attachments/assets/5af95d6c-33c4-463a-880c-db0b969e4adf)
![image](https://github.com/user-attachments/assets/5786f4d6-03d9-4e12-8ddb-75ffe13658cb)

Make a new directory for back-end.
![image](https://github.com/user-attachments/assets/be98734f-901f-482e-9e06-eaeb477425ac)

Unzip the back-end resources.
![image](https://github.com/user-attachments/assets/e8c4f892-c21f-4fd0-9b6a-70dca5420f98)

Make environment config .env to communicate with different services(Bedrock, AWS and OpenAI):

PORT=5000
AWS_REGION=us-west-2
BEDROCK_AGENT_ID=<your-bedrock-agent-id>
BEDROCK_AGENT_ALIAS_ID=<your-bedrock-agent-alias-id>
OPENAI_API_KEY=<your-openai-api-key>
OPENAI_ASSISTANT_ID=<your-openai-assistant-id>

Make a Dockerfile to set up a containerized environment for a Node.js application, installs dependencies, and specifies how to run the application:

FROM node:18
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 5000
CMD ["npm", "start"]

![image](https://github.com/user-attachments/assets/d47c7ce0-2224-437a-9ba8-b4535c0fbaaf)

Make a new directory for front-end and download front-end resources.
![image](https://github.com/user-attachments/assets/7174eb15-83e6-4ee3-ac9a-7ddbc010c42a)

Unzip the front-end resources.
![image](https://github.com/user-attachments/assets/bb62e470-e3be-459a-9e8e-33a7b1aa5cc9)

Add front-end settings for the Node.js application.

FROM node:16-alpine
WORKDIR /app
RUN npm install -g serve
COPY --from=build /app/dist /app
ENV PORT=5001
ENV NODE_ENV=production
EXPOSE 5001
CMD ["serve", "-s", ".", "-l", "5001"]

![image](https://github.com/user-attachments/assets/d5ae877d-0ca1-4552-a3dc-b1ad39589e49)

###Admin Set Up for Kubernetes

Create a user named eksuser in IAM with Administrative privileges.
![image](https://github.com/user-attachments/assets/54000981-936e-4fe5-89fe-7d697aab1a87)

Autheniticate the Admin(user).

Create access key in IAM:
Click Users> Click "eksuser" > Click "Create Access Key"
Select CLI > Check the confirmation box > Click Next
Click Create access key

Copy the Access from the Screen by clicking the copy button.
![image](https://github.com/user-attachments/assets/56bf8f56-1d58-4b33-8bbd-cee23c75d019)

Run command aws configure. It will ask you for the access key, paste it there and hit enter.
![image](https://github.com/user-attachments/assets/51b2c0e3-9d9f-4253-a49d-186d76a2d69c)

You will then need to repeat this step with the Secret access key.
![image](https://github.com/user-attachments/assets/3c83efe5-34bb-4020-8688-48dc4673ebbc)

Enter the region you are working with in the next step, for me it's us-west-2
Keep the default output format by clicking enter.

##Kubernetes Set Up

Install eksctl through the CLI.
![image](https://github.com/user-attachments/assets/4fc9a275-10ed-4766-a72e-c2a2d4f9b669)

Install kubectl through the CLI.
![image](https://github.com/user-attachments/assets/faf75f8b-a289-4970-8b8d-657a2744faa0)

###Create an EKS Cluster:

eksctl create cluster \
  --name cloudmart \
  --region us-west-2 \
  --nodegroup-name standard-workers \
  --node-type t3.medium \
  --nodes 1 \
  --with-oidc \
  --managed

![image](https://github.com/user-attachments/assets/5a8b46b6-a575-4512-b73c-89b12fc47d41)

Connect to the EKS cluster using the kubectl configuration and verify cluster connectivity.
![image](https://github.com/user-attachments/assets/8dbbfdcd-4059-438a-9c6f-6a4add7bf152)

Create a Role & Service Account to provide pods access to services used by the application (DynamoDB, Bedrock, etc):
![image](https://github.com/user-attachments/assets/53cbffa4-b08f-42d6-9a0e-db1d57cfac07)

//--> I gave the service account admin privileges for simplicity, however, in a real-world application you would follow the "Least Privilege/Zero Trust" model.

###Create an ECR Repository for the backened to house the Docker Image.

Name the repository and leave all other options as default > Click create.

![image](https://github.com/user-attachments/assets/45004723-f568-4b18-a8fd-21eafe11d34a)

//--> I am using a public repository for the lab, however, in the real-world you would use a private repository so you don't risk exposing your docker image to the public.

On the CLI switch to the backend directory.

cd ..
cd backend

Follow the steps for ECR onboarding.
![image](https://github.com/user-attachments/assets/80cdb5f4-812c-49ee-902d-299043ee4aa2)
![image](https://github.com/user-attachments/assets/9e7c6485-77d1-4cd6-a51b-6754e5d8c465)
![image](https://github.com/user-attachments/assets/fb9d651d-82b9-4c2b-bb43-9222c2410595)

Create a Kubernetes deployment file (YAML) for the Backend.
nano cloudmart-backend.yaml

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
      
The "image:" line needs to be replaced with the latest URI Image from the ECR instance we created.

Apply the .yaml configure file using the CLI tool.

kubectl apply -f cloudmart-backend.yaml
![image](https://github.com/user-attachments/assets/78e9f2d4-2621-43cf-8601-f66560b7afa5)

Monitor the status of objects being created and obtain the public IP generated for the API.
![image](https://github.com/user-attachments/assets/88c5ff87-814b-43b0-8dcc-0d1c61f90d08)

Change back to the frontend directory to work on the front end.

cd ..
cd frontend

Use the CLI to get the URL for the Kubernetes API
![image](https://github.com/user-attachments/assets/6dd00d33-5a80-4470-91c5-afd396f6af1e)

Copy the URL to appened the following:

nano .env
VITE_API_BASE_URL=http://<your_url_kubernetes_api>:5000/api

###Create an ECR Repository for the frontend.

Name the repository and leave all other options as default > Click create.

![image](https://github.com/user-attachments/assets/a3fa488d-6120-4c88-8317-b385d6c107c6)

Follow the steps for ECR onboarding like we did for the backend.
![image](https://github.com/user-attachments/assets/40ff1f55-ea25-40f6-9171-89f1b316c375)

Create a Kubernetes deployment file (YAML) for the Frontend.
nano cloudmart-frontend.yaml

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

//--> Make sure you edit the "image:" field with the correct image like we did on the backend.

Apply the deployment file through the CLI tool:
![image](https://github.com/user-attachments/assets/16b69edf-6197-4484-beda-6e491773fa6c)

Use the CLI to verify the services are running:
![image](https://github.com/user-attachments/assets/d162aac9-c885-4304-84ca-b7ecc14eea29)

This is what we have so far:

![image](https://github.com/user-attachments/assets/a2714e7f-2b52-43af-ac44-2297a308ceb2)
