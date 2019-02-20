# How to save costs in AWS EC2 Instances

![Refer](https://aws.amazon.com/premiumsupport/knowledge-center/start-stop-lambda-cloudwatch/)
Resolution
CloudWatch Events allows you to create an event that is triggered at a specified time or interval in response to events that take place in your account. For example, you can create an event using CloudWatch Events for a specific time of day, or you can create an alarm when CPU utilization for an instance reaches a specific threshold. You can also configure a Lambda function to start and stop instances when triggered by these events.

In this example, we use Lambda functions to start and stop EC2 instances, and then we use CloudWatch Events to start instances in the morning and stop the instances at night.

1.    Open the AWS Lambda console, and choose Create function.
2.    Choose Author from scratch.
3.    Enter a Name for your function, such as "StopEC2Instances."
4.    From the Runtime drop-down menu, choose Python2.7.
5.    Expand the Role drop-down menu, and then choose Create a custom role. This opens a new tab or window in your browser.
6.    In the IAM Role drop-down menu, choose Create a new IAM Role, and enter a Role Name, such as “lambda_start_stop_ec2."
7.    Expand View Policy Document, choose Edit, and then choose Ok when prompted to read the documentation. Edit the policy as follows:

```sh

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    },
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

```


8.    Choose Allow to complete the role and return to the AWS Lambda console, and then choose Create Function.
9.    To stop your instances, enter the following into the Function code editor:


function.py:
```sh

import boto3
# Enter the region your instances are in. Include only the region without specifying Availability Zone; e.g., 'us-east-1'
region = 'XX-XXXXX-X'
# Enter your instances here: ex. ['X-XXXXXXXX', 'X-XXXXXXXX']
instances = ['X-XXXXXXXX']

def lambda_handler(event, context):
    ec2 = boto3.client('ec2', region_name=region)
    ec2.stop_instances(InstanceIds=instances)
    print 'stopped your instances: ' + str(instances)

```

10.  In Basic settings, enter 10 seconds for the function Timeout.
11.  Choose Save.
12.  Repeat these steps to create another function that starts your instances again by using the following:
```sh

import boto3
# Enter the region your instances are in. Include only the region without specifying Availability Zone; e.g.; 'us-east-1'
region = 'XX-XXXXX-X'
# Enter your instances here: ex. ['X-XXXXXXXX', 'X-XXXXXXXX']
instances = ['X-XXXXXXXX']

def lambda_handler(event, context):
    ec2 = boto3.client('ec2', region_name=region)
    ec2.start_instances(InstanceIds=instances)
    print 'started your instances: ' + str(instances)


```



Note: Use a Name and Description that indicate this function is used to start instances. You can use the previously created role.

Test your functions

1.    Open the AWS Lambda console, and then choose Functions.
2.    Choose your function, and then choose Test.
3.    In Event name, type a name, and then choose Create.
4.    Choose Test to execute the function.

Note: The body of the test event doesn't affect your function, because the function doesn't use it.

Create a CloudWatch Event that triggers your Lambda function at night

1.    Open the Amazon CloudWatch console.
2.    Choose Events, and then choose Create rule.
3.    Choose Schedule under Event Source.
4.    Enter an interval of time or cron expression that tells Lambda when to stop your instances. For more information on the correct syntax, see Schedule Expression Syntax for Rules.
Note: Cron expressions are evaluated in UTC. Be sure to adjust the expression for your preferred time zone.
5.    Choose Add target, and then choose Lambda function.
6.    For Function, choose the Lambda function that stops your instances.
7.    Choose Configure details.
8.    Use the following information in the provided fields:
       For Name, type a meaningful name, such as "StopEC2Instances."
       For Description, add a meaningful description, such as “stops EC2 instances every day at night.”
       For State, choose Enabled.
       Choose Create rule.

To restart your instances in the morning, repeat these steps and use your preferred start time.


Create a cron scheduler something like this...
Cron (configures to GMT, just convert GMT to your timezone):
0 6 ? * MON-FRI *
