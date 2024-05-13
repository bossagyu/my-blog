+++
title = 'How to Schedule Lambda Functions with AWS EventBridge'
date = 2023-12-21T23:03:13+09:00
draft = false
categories = ['Engineering']
tags = ['aws', 'lambda', 'eventbridge']
+++

## Overview
This guide explains how to schedule Lambda functions using AWS EventBridge.

## What is AWS EventBridge?
AWS EventBridge is a service that facilitates event passing between AWS services. Using EventBridge enables you to build an event-driven architecture.

For more detailed information, refer to the [AWS official documentation](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html).

## Prerequisites
It is assumed that the Lambda function is already created. To learn how to create a Lambda function, please refer to [Getting Started with AWS Lambda](https://aws.amazon.com/lambda/getting-started/).

## Procedure
Select the Lambda function you plan to schedule with EventBridge and click "Add trigger."  
![Add trigger screen](img-008-001.png)

From the trigger options, select "EventBridge."  
![Selecting EventBridge screen](img-008-002.png)

After selecting the trigger, you will be directed to create a rule. For this instance, the rule is set to execute every 5 minutes using cron syntax.  
For the cron syntax, refer to the [Schedule type on EventBridge Scheduler](https://docs.aws.amazon.com/scheduler/latest/UserGuide/schedule-types.html#cron-based) page.  
![Adding a trigger](img-008-003.png)

Once the setup is complete, EventBridge will be added to the Lambda function's diagram as a trigger.  
![Setup completion screen](img-008-004.png)

For instance, I created a Function to send messages to LINE, and it started sending notifications every 5 minutes like this.

![Results of running a Lambda function with EventBridge](img-008-005.png)

## Conclusion
This guide detailed how to schedule Lambda functions using AWS EventBridge. Keep in mind that leaving the EventBridge setup as is will incur charges, so be sure to delete it when it's no longer needed.
