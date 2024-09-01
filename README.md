# Scheduling EC2 Instances for Off-Hours

Purpose : Saving costs

## Overview

To optimize costs on your AWS EC2 instances, you can schedule them to automatically start and stop during off-hours. This is especially useful for development, testing environments, or applications that do not require 24/7 availability. This guide will walk you through setting up AWS Lambda functions and CloudWatch Events to automate the scheduling of EC2 instances.

## Prerequisites

1. **AWS Account**: You need an AWS account to set up the resources.
2. **IAM Permissions**: Ensure your IAM user has permissions to create and manage Lambda functions, CloudWatch Events, and EC2 instances.
3. **AWS CLI**: Optional, but useful for managing resources from the command line.

## Requirements

- **AWS EC2**: Instances you want to schedule.
- **AWS Lambda**: To run code that starts and stops EC2 instances.
- **AWS CloudWatch Events**: To trigger Lambda functions based on a schedule.

## Step-by-Step Instructions

### 1. Create IAM Role for Lambda

1. **Navigate to IAM**:
   - Open the IAM Dashboard in the AWS Management Console.

2. **Create a Role**:
   - Click "Roles" and then "Create role".
   - Choose "AWS service" and select "Lambda".
   - Click "Next: Permissions".

3. **Attach Policies**:
   - Attach the following policies:
     - `AmazonEC2FullAccess` (for managing EC2 instances)
     - `AWSLambdaBasicExecutionRole` (for CloudWatch logging)
   - Click "Next: Tags" (optional), then "Next: Review".
   - Name the role (e.g., `LambdaEC2SchedulerRole`), and click "Create role".

### 2. Create Lambda Functions

1. **Navigate to Lambda**:
   - Open the Lambda Dashboard in the AWS Management Console.

2. **Create Lambda Function to Start Instances**:
   - Click "Create function".
   - Choose "Author from scratch".
   - Name the function (e.g., `StartEC2Instances`).
   - Select the runtime (e.g., Python 3.9).
   - Choose the execution role you created earlier (`LambdaEC2SchedulerRole`).

3. **Add Function Code**:
   - Use the following code to start EC2 instances:

```Python
   import boto3

   ec2 = boto3.client('ec2')

   def lambda_handler(event, context):
       response = ec2.start_instances(
           InstanceIds=['i-1234567890abcdef0']  # Replace with your instance IDs
       )
       return response
```

- Click "Deploy" to save the function.

4. **Create Lambda Function to Stop Instances**:
    
    - Repeat the process to create another Lambda function (e.g., `StopEC2Instances`).
    - Use the following code to stop EC2 instances:

```Python
import boto3

ec2 = boto3.client('ec2')

def lambda_handler(event, context):
    response = ec2.stop_instances(
        InstanceIds=['i-1234567890abcdef0']  # Replace with your instance IDs
    )
    return response
```

- Click "Deploy" to save the function.

### 3. Schedule Lambda Functions Using CloudWatch Events

1. **Navigate to CloudWatch**:
    
    - Open the CloudWatch Dashboard in the AWS Management Console.
2. **Create a Rule to Start Instances**:
    
    - Go to "Rules" under "Events".
        
    - Click "Create rule".
        
    - Choose "Event Source" and select "Schedule".
        
    - Define the schedule using a cron expression (e.g., `cron(0 9 * * ? *)` for starting at 9 AM UTC daily).
        
    - Click "Add target".
        
    - Choose "Lambda function" and select the `StartEC2Instances` function.
        
    - Click "Configure details".
        
    - Name the rule (e.g., `StartEC2InstancesRule`) and click "Create rule".
        
3. **Create a Rule to Stop Instances**:
    
    - Repeat the process to create another rule for stopping instances.
        
    - Define the schedule using a cron expression (e.g., `cron(0 17 * * ? *)` for stopping at 5 PM UTC daily).
        
    - Click "Add target".
        
    - Choose "Lambda function" and select the `StopEC2Instances` function.
        
    - Click "Configure details".
        
    - Name the rule (e.g., `StopEC2InstancesRule`) and click "Create rule".
        

## Conclusion

By following these steps, you have set up an automated system to start and stop EC2 instances based on a predefined schedule. This setup helps you save costs by ensuring instances run only when needed. The Lambda functions manage the instance state changes, and CloudWatch Events ensure they run at the specified times.

## Additional Resources

- [AWS Lambda Documentation](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
- [Amazon EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [AWS CloudWatch Documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html)
- [AWS IAM Documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/)
