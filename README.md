# MultiCloud-AI
Project for MultiCloud-AI.
Provision a VM with EC2
![image](https://github.com/user-attachments/assets/f6f8e75c-e9a4-46cd-b480-c0cac552cb6c)
Launch EC2 Instance
![image](https://github.com/user-attachments/assets/1c208424-1f25-4ebf-ae0b-770db497a741)
Install Terraform
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

Initialize Terraform
![image](https://github.com/user-attachments/assets/aabee7cc-d647-4064-a3ad-3e7d78b10ec0)

Use terraform plan to show what our config file will be doing
![image](https://github.com/user-attachments/assets/9e9dca6f-6162-4923-9a35-9ce30f66dddd)

Apply via Terraform to automate the S3 bucket creation
![image](https://github.com/user-attachments/assets/564daa6a-4377-4b7d-b945-74ea189c95e6)
Veryify in s3 that the bucket was created.
![image](https://github.com/user-attachments/assets/7ba5e91f-c59a-4bfb-afe5-1cfc37e73bdd)

Edit the main.tf file to create tables in the S3 bucket.
![image](https://github.com/user-attachments/assets/f4697f46-cbcf-4b3b-86d3-5fce97c07e4a)

Apply Terraform to automate the table creation and remove the S3 bucket.
![image](https://github.com/user-attachments/assets/1fa6b58c-7a64-412a-a469-f6f5da42939d)
