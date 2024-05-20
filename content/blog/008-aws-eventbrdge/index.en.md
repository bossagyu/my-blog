+++
title = 'Using AWS EventBridge to Schedule Lambda Functions'
date = 2023-12-21T23:03:13+09:00
draft = true
categories = ['Engineering']
tags = ['AWS', 'Lambda', 'EventBridge']
+++

## Overview
This article explains how to use AWS EventBridge to schedule Lambda functions for periodic execution.

## What is AWS EventBridge?
AWS EventBridge is a service that facilitates the passing of events between AWS services, enabling the creation of event-driven architectures. For a detailed explanation, refer to the [AWS official documentation on EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html).

## Prerequisites
It is assumed that you have already created a Lambda function. For instructions on creating Lambda functions, please see the [AWS Lambda Getting Started guide](https://aws.amazon.com/lambda/getting-started/).

## Steps to Schedule a Lambda Function with EventBridge
To schedule your Lambda function with EventBridge, follow these steps:

1. **Select the Lambda Function:** Open the AWS Management Console, navigate to the Lambda service, and select the function you want to schedule.

   ![Trigger addition screen](img-008-001.png)

2. **Add a Trigger:** Choose 'Add trigger' and select 'EventBridge (CloudWatch Events)' from the list of available triggers.

   ![Selecting EventBridge](img-008-002.png)

3. **Create a Rule:** Once you select EventBridge, you will be prompted to create a new rule. Set up the rule to trigger the Lambda function at desired intervals. For this tutorial, we are using a cron expression to execute the function every 5 minutes. You can find more about cron syntax in the [EventBridge Scheduler documentation](https://docs.aws.amazon.com/scheduler/latest/UserGuide/schedule-types.html#cron-based).

   ![Trigger configuration](img-008-003.png)

4. **Complete the Setup:** After configuring the rule, it will appear in the Lambda function's diagram as an associated trigger.

   ![Configuration completion screen](img-008-004.png)

5. **Test the Setup:** As a practical application, I set up a function to send messages to LINE. The setup now sends notifications every 5 minutes, as shown in the screenshot below.

   ![Notification from EventBridge-triggered Lambda function](img-008-005.png)

## Conclusion
We've outlined how to use AWS EventBridge to schedule Lambda functions periodically. Remember, leaving the EventBridge setup as is will incur charges, so be sure to delete it if it's no longer needed.