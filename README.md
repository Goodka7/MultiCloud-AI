<img src="https://github.com/user-attachments/assets/c584d492-45a7-4964-b7ea-c9d7e13eba49" width="500" />
<br></br>
<img src="https://github.com/user-attachments/assets/23065407-8251-467c-b7dd-17e617b8832f" width="500" />

# STAGE 1 - Foundational Services Set Up

## AWS Virtual Machine Set Up

### First I provisioned a VM using EC2

<img src="https://github.com/user-attachments/assets/f6f8e75c-e9a4-46cd-b480-c0cac552cb6c" width="800" />

### Next, I launched the EC2 Instance to make use of the CLI

<img src="https://github.com/user-attachments/assets/1c208424-1f25-4ebf-ae0b-770db497a741" width="800" />

---

## Terraform Set Up

### Using the CLI, I installed Terraform on my EC2 VM

<img src="https://github.com/user-attachments/assets/1907d4ad-3a76-4100-88b4-5b7cd224255d" width="800" />

### I then used nano to create a config for a S3 bucket called `main.tf`:

```nano
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

### I used `terraform init` I initialized Terraform.

<img src="https://github.com/user-attachments/assets/aabee7cc-d647-4064-a3ad-3e7d78b10ec0" width="800" />

### I then used `terraform plan` to review and make sure the services are configured correctly

<img src="https://github.com/user-attachments/assets/9e9dca6f-6162-4923-9a35-9ce30f66dddd" width="800" />

### Next I used `terraform apply` to automate the S3 bucket creation

<img src="https://github.com/user-attachments/assets/564daa6a-4377-4b7d-b945-74ea189c95e6" width="800" />

### After that, I manually verified in S3 that the bucket was created

<img src="https://github.com/user-attachments/assets/7ba5e91f-c59a-4bfb-afe5-1cfc37e73bdd" width="800" />

---

## DynamoDB Set Up

### I editted the `main.tf` file so I can automate the creation of DynamoDB tables

<img src="https://github.com/user-attachments/assets/f4697f46-cbcf-4b3b-86d3-5fce97c07e4a" width="800" />

### Using `terraform apply` I automate the table creation and remove the S3 bucket

<img src="https://github.com/user-attachments/assets/1fa6b58c-7a64-412a-a469-f6f5da42939d" width="800" />

---

## Docker Set Up

### Install Docker

<img src="https://github.com/user-attachments/assets/5af95d6c-33c4-463a-880c-db0b969e4adf" width="800" />
<img src="https://github.com/user-attachments/assets/5786f4d6-03d9-4e12-8ddb-75ffe13658cb" width="800" />

### Make a new directory for the back-end

<img src="https://github.com/user-attachments/assets/be98734f-901f-482e-9e06-eaeb477425ac" width="800" />

### Unzip the back-end resources

<img src="https://github.com/user-attachments/assets/e8c4f892-c21f-4fd0-9b6a-70dca5420f98" width="800" />

### Make environment config `.env` to communicate with different services(`Bedrock`, `AWS` and `OpenAI`):

```Nano
PORT=5000
AWS_REGION=us-west-2
BEDROCK_AGENT_ID=<your-bedrock-agent-id>
BEDROCK_AGENT_ALIAS_ID=<your-bedrock-agent-alias-id>
OPENAI_API_KEY=<your-openai-api-key>
OPENAI_ASSISTANT_ID=<your-openai-assistant-id>
```

<img src="https://github.com/user-attachments/assets/d47c7ce0-2224-437a-9ba8-b4535c0fbaaf" width="800" />

### Make a `Dockerfile` to set up a containerized environment for a `Node.js` application, installs dependencies, and specifies how to run the application:

```Nano
FROM node:18
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 5000
CMD ["npm", "start"]
```

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

Here we have a working API that we can add products to:

![image](https://github.com/user-attachments/assets/6e0c72df-f7e3-422c-b627-8696f624b7ec)
![image](https://github.com/user-attachments/assets/ce839899-bbb4-4d03-bb95-d87ef736accb)


###Create a GitHub repository so we can use it for a CI/CD pipeline to the Multicloud Enviroment.

Created a public repository on github called "cloudmart", then in the CLI:

echo "# cloudmart" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/Goodka7/cloudmart.git
git push -u origin main

Generate a password token on GitHub for the last step and use that token for the password to push the origin.
![image](https://github.com/user-attachments/assets/6ae9dfe9-a235-46e9-8fe8-29104c350a8b)

Use the token in the password field.
![image](https://github.com/user-attachments/assets/f9e7ad8a-f98c-49ab-9501-8a44579a9f4a)
![image](https://github.com/user-attachments/assets/aa85b7ca-de78-4353-b552-d5e544af5c36)

Add git files to the repository.

git add -A
git commit -m "app sent to repo"
![image](https://github.com/user-attachments/assets/4ab3478b-7e7c-474c-9fbf-fc3f11548c5d)

git push
![image](https://github.com/user-attachments/assets/856ef0a9-ee39-43cf-ab33-a7c035b64b62)
![image](https://github.com/user-attachments/assets/6ac42db8-6fe1-42b7-a1fc-3204e6505e8b)

Create the Codepipeline

Access AWS CodePipeline.
Start the 'Create pipeline' process.
Name: `cloudmart-cicd-pipeline`
Use the GitHub repository `cloudmart` as the source.
![image](https://github.com/user-attachments/assets/1aaaca58-98bb-4fdf-80f0-750c92373937)

//--> Normally you would choose the GitHub (GitHub App) authetication, but because this is a lab we are going to use OAUTH.
Use the GitHub Branch `main` as the source.

Set build provider to "Other build providers" and select `AWSCodeBuild`
Make sure `SourceArtifact` is set for Input Artifact
![image](https://github.com/user-attachments/assets/93dc4d91-02c1-48f1-b083-8b0b8b8ec3c4)


Click "Create Project"

Give the project the name cloudmartBuild.
Select amazonlinux-x86_64-standard:4.0 as the Image.
![image](https://github.com/user-attachments/assets/588c0023-4ad2-454d-9fe6-5b046e5208f2)

Configure the environment to support Docker builds by checking "Enable this flag if you want to build Docker images or want your builds to get elevated privileges"
![image](https://github.com/user-attachments/assets/5e015e61-dc48-4e0c-be58-860a490f528a)

Add the environment variable **ECR_REPO** with the ECR front end repository URI. //---> Do NOT use :latest as this will give you an error.
![image](https://github.com/user-attachments/assets/0bb21ca3-2033-400c-a431-b5f2ecf7d65b)

For the build specification:

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

//--> The "project build creator" will ask you if you're okay to "Leave" the site, and suggests your options won't be saved, click "Leave".

![image](https://github.com/user-attachments/assets/02e20878-63c9-436d-b418-20cce7416b2a)

Click "Create Pipeline"

Set permission AmazonElasticContainerRegistryPublicFullAccess for role created by the pipeline process, in IAM.
![image](https://github.com/user-attachments/assets/f09789d2-60d1-48af-98cd-5f8213fed46c)

Continue through by skipping the next few screens with "Next", it will be the Test and Deploy screens (likely).

The pipeline will execute the build.
![image](https://github.com/user-attachments/assets/865dd6d4-0e26-4361-8444-fc311e75ac49)

//---> The "build" portion will fail if you do not set up the permission in the role before running, if so, just run again.

Next we need to set up the deployment, click "Edit" on the pipeline page.

After "build" click "+Add Stage".
![image](https://github.com/user-attachments/assets/2bf9a7bd-9d39-4aa7-83be-a71fac81976d)
![image](https://github.com/user-attachments/assets/eea203f2-9ac1-44a8-9720-ebfbb51f3aea)

Click "add action group".
![image](https://github.com/user-attachments/assets/97523f5e-2ab3-49e2-93d4-77bdb5307497)

Once all this information is entered, click "Create project"
Title the project: cloudmartDeployToProduction
Scroll down to image and make sure amazonlinux-x86_64-standard:4.0 is selected.
We will create two Enviromental Variables: AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY so we can authenticate to the Kubernetes cluster.
You will need to enter the access key values for the eksuser that we created earlier, however you can create new keys if you lost them.
![image](https://github.com/user-attachments/assets/53deaeb5-2bd8-462d-bd1f-a5e8dfee8393)

//---> Normally you would not do this in a production environment as it doesn't follow security best practices.

Scroll down to build spec and click "switch to Editor":

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

This build script allows for the kubernetes to search the imagedefinitions.json file to find the current imageUri, then then change the value of the Uri variable inside cloudmart-frontend.yaml
However, we currently have the value hard coded and do not have a variable in place.

nano cloudmart-frontend.yaml

Change the hardcoded value to CONTAINER_IMAGE.
![image](https://github.com/user-attachments/assets/3ee566f1-28fe-4e97-a438-69b4a3446ab1)

Commit and push changes.

git add -A
git commit -m "replaced image uri with CONTAINER_IMAGE"
git push

![image](https://github.com/user-attachments/assets/fb1b3711-bba5-424c-9b7f-c3865c2a8009)

### Test the CI/CD Pipeline

Make a Change on GitHub:
Update the application code in the `cloudmart` repository.
Append file `src/components/MainPage/index.jsx` line 93 to say "Featured Products on Cloudmart" 
![image](https://github.com/user-attachments/assets/ea5c6a6b-2c7c-445f-b799-c33c1812f905)
![image](https://github.com/user-attachments/assets/c7573990-708a-4c31-b734-c6f33e26f6bd)

Before:
![image](https://github.com/user-attachments/assets/6932516a-1e2b-49cb-9f65-2e65d624de27)

Commit and push the changes.
    
  git add -A
  git commit -m "changed to Featured Products on CloudMart"
  git push

![image](https://github.com/user-attachments/assets/c66256f6-3a55-459c-a7f8-123f744e4ad6)

After:
![image](https://github.com/user-attachments/assets/978b2dfb-fa4e-4d17-bc94-1da504fd0fc7)

We can see that the pipeline is working as the change was accepted.


PART 3

First lets copy the products.zip into the Terraform folder

cd challenge-day2/backend/src/lambda
cp list_products.zip ../../../../terraform-project/
cd ../../../../terraform-project

Next we will append the main.tf:

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

This script creates an IAM role and policy for a Lambda function, the Lambda function itself (which lists products from DynamoDB), permissions for Bedrock to invoke the function, and outputs the ARN of the Lambda function.

terraform apply in order to push changes.
![image](https://github.com/user-attachments/assets/743c2081-d185-47fb-affc-68557a25fff9)

Set up Amazon Bedrock
\\---> We will be using the Claude 3 Sonnet verison even though its considered a legacy, this is because the set up is very easy.
You must request the agent, you can do so by searching the list for the agent you wish to use.
![image](https://github.com/user-attachments/assets/7a7ef9fa-b296-45dc-9bbf-6baefaa04bf0)

Select "Agent" under "Builder Tools" on the left side of the screen.
Click "Create Agent".
![image](https://github.com/user-attachments/assets/f0205cd9-192c-46d4-af26-dde9a1fffe38)

Insert the following for Agent Instructions:

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

Scroll up and click "Save and Exit"
Scroll down on the cloudmart-product-recommendation-agent overview

![image](https://github.com/user-attachments/assets/94947079-53f3-4428-b560-67a2ff5559fc)

Click the link to open in a NEW TAB.
We are going to add a permission so the Agent can use the Lambda function.
Click "Add permissions".
Select "Create inline policy".
Select "json" at the top and add the following script:

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

Click "Next".
Name the policy `BedrockAgentLambdaAccess`.
Click "Create policy".
![image](https://github.com/user-attachments/assets/6f07ef5d-dfe4-4e56-b67a-2c5971a310a1)

Time to add an Action Group.
Going back to the Amazon Bedrock screen, click `Edit in Agent Builder`.
Scroll down to the Action Group section and click `Add`.
Set the Action Group name to `Get-Product-Recommendations`.
Set the action group type as `Define with API schemas`.
Select the Lambda function `cloudmart-list-products` as the action group executor.
In the `Action group schema` section, choose `Define via in-line schema editor`.
Paste the OpenAPI schema below into the schema editor:

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
Click on `Prepare Agent` to finalize the creation.

Test the Agent
![image](https://github.com/user-attachments/assets/b56a9082-ba28-48c6-8e62-1b593cdf4edf)

On the agent details page, go to the `Aliases` section.
Click on `Create alias`.
Name the alias `cloudmart-prod`.
Select the most recent version of the agent.
Click on `Create alias` to finalize.

##Set up OpenAI Agent

Access the OpenAI platform (https://platform.openai.com/).
Log in.
Navigate to the "Assistants" section.
Click on "Create New Assistant".
Name the assistant "CloudMart Customer Support".
Select the model `gpt-4o`.
In the "Instructions" section, paste the following:

```Text
You are a customer support agent for CloudMart, an e-commerce platform. Your role is to assist customers with general inquiries, order issues, and provide helpful information about using the CloudMart platform. You don't have direct access to specific product or inventory information. Always be polite, patient, and focus on providing excellent customer service. If a customer asks about specific products or inventory, politely explain that you don't have access to that information and suggest they check the website or speak with a sales representative.
```

Enable "Code Interpreter" so that the assistant is able to help with technical aspects of using the platform.
Note down the Assistant ID: `asst_LmaOL5srxUrfGXS6WALgSmwO`

Go to the API Keys section in your OpenAI account.
Generate a new API key.
Copy this key, you'll need it for your environment variables.

Switching back to the EC2 instance we need to change directories.

cd ../..
cd challenge-day2/backend

Now we will append the cloudmart-backend.yaml file to allow our AI access.
We will be giving the placeholder values actual values from both our AI Agent and AI Assistant.

nano cloudmart-backend.yaml


        - name: BEDROCK_AGENT_ID
          value: "xxxx"
        - name: BEDROCK_AGENT_ALIAS_ID
          value: "xxxx"
        - name: OPENAI_API_KEY
          value: "xxxx"
        - name: OPENAI_ASSISTANT_ID
          value: "xxxx"
          
![image](https://github.com/user-attachments/assets/5f8afd54-14f9-4cfa-9b9d-8d5f7d2c6c37)

We find this info on the OpenAI assistant page as well as the Bedrock agent page.

Now we need to push the appended .yaml into the environment

![image](https://github.com/user-attachments/assets/9b7f0054-803c-4e81-94d5-c2178de5df35)

### Test the AI

Bedrock AI Agent:
![image](https://github.com/user-attachments/assets/6830d70e-cab9-436f-8a40-a5e5c49973bf)

Open AI Assistant:
![image](https://github.com/user-attachments/assets/0899df3b-569c-44c9-9f3b-397abb3e65f1)

Here we have two functioning AI bots that are capable of handling customer queries and even cancel orders:

![image](https://github.com/user-attachments/assets/7db8b10a-891d-424a-a87d-299c57f69f83)
![image](https://github.com/user-attachments/assets/829fa8b3-20bf-4b2f-b0ed-90e5b83b5297)
![image](https://github.com/user-attachments/assets/008347bc-2576-4dc9-9bd9-a7f5d704c762)
![image](https://github.com/user-attachments/assets/74958021-b027-4808-af82-aa7a418235d9)


