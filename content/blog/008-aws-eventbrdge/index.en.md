+++
title = 'How to Schedule Lambda Functions with AWS EventBridge'
date = 2023-12-21T23:03:13+09:00
draft = false
categories = ['Engineering']
tags = ['aws', 'lambda', 'eventbridge', 'serverless']
+++

## Overview

This article explains how to use AWS EventBridge to schedule Lambda functions for periodic execution.

I used this approach to implement scheduled reminder notifications for a LINE Bot.
For example, you can send cleaning reminders every morning at 7 AM without managing any servers.

## What is AWS EventBridge?

AWS EventBridge is a service that facilitates event passing between AWS services.
It has two main use cases:

| Use Case | Description |
|----------|-------------|
| Scheduled Execution | Run Lambda periodically using cron or rate expressions |
| Event-Driven | Trigger Lambda on events like S3 file uploads |

This article focuses on scheduled execution.

## Real-World Use Cases

EventBridge + Lambda scheduled execution is useful for:

- **Reminder notifications**: Send daily notifications to Slack or LINE at fixed times
- **Batch processing**: Aggregate data hourly and save to database
- **Health checks**: Monitor external API availability every 5 minutes
- **Cleanup tasks**: Delete old logs every night

I use this for my [Cleaning Reminder Bot](/blog/045-clean-bot/) to check notification conditions every hour.

## Prerequisites

This guide assumes you have already created a Lambda function.
For instructions on creating Lambda functions, see the [AWS Lambda Getting Started guide](https://aws.amazon.com/lambda/getting-started/).

## Steps

### 1. Add a Trigger

Select the Lambda function you want to schedule with EventBridge and click "Add trigger".

![Trigger addition screen](img-008-001.png)

### 2. Select EventBridge

Choose "EventBridge (CloudWatch Events)" from the trigger options.

![Selecting EventBridge](img-008-002.png)

### 3. Configure the Schedule

After selecting EventBridge, you'll be prompted to create a rule. Configure the schedule as needed.

![Trigger configuration](img-008-003.png)

### 4. Setup Complete

Once configured, EventBridge will appear as a trigger in the Lambda function diagram.

![Configuration completion screen](img-008-004.png)

## Cron Expression Syntax

EventBridge cron expressions consist of 6 fields:

```
cron(minute hour day month day-of-week year)
```

### Common Patterns

| Timing | Cron Expression |
|--------|-----------------|
| Daily at 9 AM (UTC) | `cron(0 9 * * ? *)` |
| Daily at 9 AM (JST = UTC+9) | `cron(0 0 * * ? *)` |
| Every hour at minute 0 | `cron(0 * * * ? *)` |
| Every 5 minutes | `cron(0/5 * * * ? *)` |
| Weekdays only at 9 AM (UTC) | `cron(0 9 ? * MON-FRI *)` |
| First day of each month at midnight (UTC) | `cron(0 0 1 * ? *)` |

### Note: Timezone is UTC

EventBridge cron expressions use **UTC timezone**. To schedule in JST (Japan Standard Time), subtract 9 hours.

Example: Run at 7 AM JST → `cron(0 22 * * ? *)` (10 PM UTC the previous day)

## Testing the Setup

I set up a function to send messages to LINE. The setup now sends notifications every 5 minutes:

![Notification from EventBridge-triggered Lambda function](img-008-005.png)

For testing, set a short interval (like 5 minutes) to verify it works, then change to an appropriate interval for production.

## Cost

The EventBridge + Lambda combination is very cost-effective:

| Service | Free Tier | Cost After Free Tier |
|---------|-----------|---------------------|
| EventBridge | Free | Schedule rules are free |
| Lambda | 1M requests/month free | $0.20 per 1M requests |

For personal projects like reminder bots, you can run nearly for free.

## Common Pitfalls

### 1. Timezone Confusion

As mentioned, EventBridge uses UTC. A common mistake is setting "9 AM" and finding it runs at 6 PM instead.

### 2. Initial Execution Timing

After setting up a cron expression, you need to wait for the next scheduled time.
To test immediately, use Lambda's built-in test feature.

### 3. Don't Forget to Delete

Test EventBridge rules left running will keep triggering Lambda.
Always delete rules you no longer need.

## Summary

This article covered how to schedule Lambda functions with AWS EventBridge.

- EventBridge supports both scheduled execution and event-driven triggers
- Cron expressions use UTC timezone - subtract 9 hours for JST
- Running costs are nearly free for personal projects
- Don't forget to delete unused rules

Serverless scheduled execution is great for reminder bots and batch processing. Give it a try!

## Related Articles

- [How to Use the Cleaning Reminder Bot](/blog/045-clean-bot/)
- [Building a Cleaning Reminder Bot - Technical Guide](/blog/046-clean-bot-technical/)
