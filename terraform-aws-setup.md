Step-by-Step Instructions to Set Up a Basic EC2 Instance using Terraform
Prerequisites
Terraform Installed: Ensure Terraform is installed on your system. You can download it from the official Terraform website.

AWS CLI Installed and Configured: AWS CLI must be installed and configured with your AWS credentials and default region. You can download it from the official AWS CLI website.

AWS Account: Ensure you have an AWS account that has permissions to create EC2 instances.

Steps to Set Up a Basic EC2 Instance
Create a Project Directory:

Create a new directory for your Terraform project.
Navigate to this directory, as all your configuration files will be stored here.
Initialize Terraform Configuration:

Inside your project directory, create a file main.tf.
This file will hold the Terraform configuration for setting up your EC2 instance.
Configure the Provider:

Specify the AWS provider in your main.tf. The provider configuration will include the access credentials and region details.
Define the EC2 Instance:

In the main.tf file, define the resource block for creating a simple EC2 instance.
Specify details such as the AMI ID, instance type, key pair name, security group, and subnet (if required).
Configure Security Group:

Security groups are essential for managing inbound and outbound traffic to your instance.
Define a security group resource block in the main.tf file to allow SSH access to the instance on port 22.
Add Output Values:

Add output blocks to your main.tf file to capture and display essential details such as the Public IP address of the instance once it’s created.
Initialize Terraform:

Open your terminal, navigate to your project directory, and run terraform init to initialize your Terraform workspace. This downloads necessary providers and sets up your environment.
Validate the Configuration:

Execute terraform validate to check the syntax and configuration file for errors.
Create an Execution Plan:

Run terraform plan to create an execution plan, which outlines the actions Terraform will take to set up your infrastructure.
Apply the Terraform Configuration:

Execute terraform apply to create the EC2 instance and other resources defined in your configuration file. Confirm the action when prompted.
Verify the Setup:

Once the terraform apply completes, check your AWS Management Console to ensure the EC2 instance is running and configured according to your specifications.
Cleanup Resources:

When you are done with the instance and no longer need it, use terraform destroy to clean up all resources created by Terraform. This will help you avoid unnecessary costs.
Documenting the Setup:

Ensure you document any additional parameters or configurations used during your setup in the terraform-aws-setup.md file.
This helps maintain a reference and makes it easier for others to understand the configuration.