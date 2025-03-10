. Understand the Prerequisites
•	AWS Account: Ensure you have an AWS account with necessary permissions to create and manage resources.
•	AWS CLI Installed and Configured: AWS CLI should be installed and configured with your AWS credentials.
2. Create a CloudFormation Template File
1.	Create a Project Directory:
o	Open your terminal or command prompt.
o	Navigate to the directory where you want to create your CloudFormation project.
o	Create a new directory for your project.
mkdir cloudformation-aws-project
cd cloudformation-aws-project
2.	Create the CloudFormation Template File:
o	Create a new file named template.yaml in your project directory.
touch template.yaml
3. Define the CloudFormation Template
1.	Define the AWSTemplateFormatVersion:
o	Specify the template format version at the top of the file.
AWSTemplateFormatVersion: '2010-09-09'
2.	Add Descriptions and Parameters:
o	Include a description of the stack and define parameters for flexibility.
Description: Basic CloudFormation Template to Create an EC2 Instance

Parameters:
  KeyName:
    Description: Name of an existing EC2 key pair for SSH access
    Type: String
  InstanceType:
    Description: EC2 instance type
   Type: String
    Default: t2.micro
    AllowedValues: [t2.micro, t2.small, t2.medium]
    ConstraintDescription: Must be a valid EC2 instance type.
3.	Define the Resources:
o	Add resources such as the EC2 instance and a security group.
Resources:
 EC2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
     InstanceType: !Ref InstanceType
      KeyName: !Ref KeyName
      ImageId: ami-0c55b159cbfafe1f0
     SecurityGroups: [!Ref InstanceSecurityGroup]
  InstanceSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH access
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: '0.0.0.0/0'
4.	Add Outputs:
o	Define outputs to display key information about the created resources.
Outputs:
  InstanceId:
    Description: The instance ID
    Value: !Ref EC2Instance
  InstancePublicIp:
    Description: The public IP address of the EC2 instance
   Value: !GetAtt EC2Instance.PublicIp
4. Deploy the CloudFormation Stack
1.	Validate the Template:
o	Use the AWS CLI to validate the CloudFormation template file.
aws cloudformation validate-template --template-body file://template.yaml
2.	Create the CloudFormation Stack:
o	Use the AWS CLI to create the stack using the validated template.
aws cloudformation create-stack --stack-name my-ec2-instance-stack --template-body file://template.yaml --parameters ParameterKey=KeyName,ParameterValue=<your-key-pair-name>
o	Replace <your-key-pair-name> with the name of your existing EC2 key pair.
3.	Monitor the Stack Creation:
o	Track the stack creation process through the AWS Management Console or by using the AWS CLI.
aws cloudformation describe-stacks --stack-name my-ec2-instance-stack
5. Verify the Setup
1.	Check the Outputs:
o	Once the stack is created successfully, use the AWS CLI to view the stack outputs and find the public IP of the EC2 instance.
aws cloudformation describe-stacks --stack-name my-ec2-instance-stack --query "Stacks[0].Outputs"
2.	Log in to the Instance:
o	Use the public IP address to SSH into the EC2 instance.
ssh -i /path/to/your-key-pair.pem ec2-user@<public-ip-address>
6. Cleanup Resources
1.	Delete the CloudFormation Stack:
o	When you no longer need the resources, delete the stack to avoid unnecessary costs.
aws cloudformation delete-stack --stack-name my-ec2-instance-stack
