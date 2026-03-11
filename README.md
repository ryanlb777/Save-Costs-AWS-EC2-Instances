# AWS EC2 Cost Saver: Schedule Instance Start/Stop with Lambda and EventBridge

This README provides a step-by-step guide to reduce AWS EC2 costs by automatically starting and stopping instances on a schedule. We use AWS Lambda functions triggered by Amazon EventBridge (formerly CloudWatch Events) to achieve this.

**Reference:** [AWS Knowledge Center: Start/stop instances using Lambda and EventBridge](https://aws.amazon.com/premiumsupport/knowledge-center/start-stop-lambda-cloudwatch/)

## Overview
EventBridge allows you to create scheduled events that trigger at specific times or intervals. In this setup:
- Create Lambda functions to start and stop EC2 instances.
- Use EventBridge rules to trigger these functions (e.g., start in the morning, stop at night).

This is ideal for non-production instances that don't need to run 24/7.

## Prerequisites
- AWS account with EC2 instances.
- Permissions to create Lambda functions, IAM roles, and EventBridge rules.
- Note: Update placeholders like region (e.g., `us-east-1`) and instance IDs (e.g., `['i-0123456789abcdef0']`).

## Step 1: Create IAM Role for Lambda
1. Open the IAM console.
2. Choose **Roles** > **Create role**.
3. Select **Trusted entity type**: AWS service > Lambda.
4. Attach the `AWSLambdaBasicExecutionRole` policy.
5. Add an inline policy for EC2 actions:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "ec2:Start*",
           "ec2:Stop*"
         ],
         "Resource": "*"
       }
     ]
   }
