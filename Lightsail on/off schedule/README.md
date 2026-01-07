AWS Lightsail Auto Start/Stop Scheduler
This repository contains an AWS CloudFormation template that automatically starts and stops Amazon Lightsail instances on a schedule using instance tags, with SNS email notifications and a 5-minute execution window to prevent repeated actions.
The solution is serverless, DST-safe, and cost-efficient, making it ideal for development, test, and non-production Lightsail environments.
Features
Per-instance scheduling via tags
Timezone-aware scheduling (Europe/Dublin, DST safe)
5-minute execution window to avoid duplicate start/stop actions
SNS email notifications when instances start or stop
Default start/stop times with per-instance overrides
Fully serverless (Lambda + EventBridge)
Least-privilege IAM permissions

How It Works
EventBridge triggers the scheduler Lambda every 5 minutes.
The Lambda function:
Retrieves all Lightsail instances
Filters instances using a configurable tag (default: AutoSchedule=true)
Reads StartTime and StopTime tags (or defaults if not set)
Compares the current time (timezone-aware) against the schedule
If the current time falls within the 5-minute window, the instance is:
Started if stopped
Stopped if running
An SNS notification is sent when an action occurs.
Instance Tag Configuration

Apply the following tags to any Lightsail instance you want to manage:
Tag Key	Value Example
AutoSchedule	true
StartTime	08:00
StopTime	20:00

If StartTime or StopTime is not present, the stack default values are used.
Time format must be HH:MM (24-hour).
Default Schedule Parameters
Parameter	Default
Default Start Time	08:00
Default Stop Time	20:00
Execution Frequency	Every 5 minutes
Timezone	Europe/Dublin
Notifications

Notifications are sent via Amazon SNS
Email subscriptions must be confirmed after deployment
Notifications are sent only when a start or stop action occurs

Deployment
Using AWS CLI
aws cloudformation deploy \
  --template-file template.yaml \
  --stack-name lightsail-scheduler \
  --parameter-overrides NotificationEmail=you@example.com \
  --capabilities CAPABILITY_NAMED_IAM

Using AWS Console
Open CloudFormation
Create a new stack using the provided template
Provide a valid email address for notifications
Confirm the SNS subscription email

Requirements
AWS account with Lightsail instances
AWS Lambda (Python 3.10)
Amazon EventBridge
Amazon SNS

Limitations
Overnight schedules (for example, 22:00 to 06:00) are not supported
All times must be within the same calendar day
Schedule evaluation occurs every 5 minutes

Use Cases
Reduce costs by stopping Lightsail instances outside business hours

Automatically start development environments each morning

Enforce consistent on/off schedules across multiple instances
