+++
title = 'Building a Cleaning Reminder Bot with AWS Lambda + LINE'
date = 2026-02-07T19:00:00+09:00
draft = false
categories = ['Engineering']
tags = ['LINE BOT', 'AWS Lambda', 'Python', 'SAM', 'S3']
+++

## Overview

I built a cleaning reminder LINE Bot for family use. This article explains the technical implementation details.

See the completed Bot here: [Cleaning Reminder Bot](/blog/045-clean-bot/)

## Architecture

```
┌─────────────┐     ┌─────────────┐     ┌─────────────────────┐
│  LINE User  │────▶│ API Gateway │────▶│ Lambda              │
└─────────────┘     └─────────────┘     │ (process_user_      │
                                        │  message)           │
                                        └──────────┬──────────┘
                                                   │
┌─────────────┐     ┌─────────────────────┐       │
│  LINE User  │◀────│ Lambda              │       │
│  (notify)   │     │ (push_message_      │       ▼
└─────────────┘     │  periodically)      │  ┌─────────┐
                    └──────────┬──────────┘  │   S3    │
                               │             │ (JSON)  │
┌─────────────┐                │             └─────────┘
│ EventBridge │────────────────┘
│ (hourly)    │
└─────────────┘
```

### Services Used

| Service | Purpose |
|---------|---------|
| API Gateway (HTTP API) | Receive LINE Webhooks |
| Lambda | Message processing, scheduled notifications |
| S3 | Store per-user task data |
| EventBridge | Scheduled execution |

### Why This Architecture

- **Serverless**: Pay only for what you use, zero operational overhead
- **S3**: Cheaper than RDS, simple JSON management
- **SAM**: Infrastructure as Code for deployment management

## Project Structure

```
clean-bot/
├── lib/                      # Core modules
│   ├── clean_task.py         # Task state management
│   ├── message.py            # Command parser
│   ├── line.py               # LINE API
│   └── s3_client.py          # S3 operations
├── test/                     # Tests
├── line_clean_bot.py         # Lambda entry point
├── template.yaml             # SAM template
└── Makefile                  # Development commands
```

## Implementation Details

### 1. Lambda Entry Points

Two Lambda functions are defined.

```python
# line_clean_bot.py

def process_user_message(event, context):
    """LINE message webhook handler"""
    body = json.loads(event.get('body'))

    # Get user ID or group ID
    if body['events'][0]['source']['type'] == 'user':
        line_id = body['events'][0]['source']['userId']
    else:
        line_id = body['events'][0]['source']['groupId']

    message = body['events'][0]['message']['text']

    # Fetch user data from S3
    s3client = S3client(BUCKET_NAME)
    obj_key = line_id + '.json'
    if not s3client.check_exist_object(obj_key):
        s3client.update_object(obj_key, '{"tasks": []}')

    # Process message and reply
    # ...

def push_message_periodically(event, context):
    """Send reminder notifications on schedule"""
    current_time = datetime.now() + timedelta(hours=9)  # JST

    s3client = S3client(BUCKET_NAME)
    for obj_list in s3client.list_objects():
        clean_task = CleanTask(s3client.get_object_body(obj_list.key))

        # Check if notification conditions are met
        if clean_task.should_notify(current_time):
            # Send message
            # ...
```

### 2. Task Deadline Management

Tasks are evaluated based on "days since last completed."

```python
# lib/clean_task.py

def __evaluate_cleanup_timing(self, task):
    """Determine if task is overdue"""
    task_time = datetime.strptime(task['updated_at'], self.date_format)
    return (self.now - task_time).days >= int(task['duration'])

def get_todo_tasks(self):
    """Get list of overdue tasks"""
    return [task for task in self.tasks
            if not task.get('paused', False)
            and self.__evaluate_cleanup_timing(task)]
```

### 3. Notification Logic

Notifications are sent only on user-specified days and times.

```python
def should_notify(self, current_time):
    """Determine if notification should be sent"""
    if not self.notification['enabled']:
        return False

    # Check day of week
    weekday_map = {0: 'Mon', 1: 'Tue', 2: 'Wed', 3: 'Thu', 4: 'Fri', 5: 'Sat', 6: 'Sun'}
    current_weekday = weekday_map[current_time.weekday()]
    if current_weekday not in self.notification['days']:
        return False

    # Check time
    if current_time.hour < self.notification['hour']:
        return False

    # Check if already notified today
    # ...

    return True
```

### 4. Command Parser

Commands are parsed using simple string splitting.

```python
# lib/message.py

def get_return_message(self, message, s3client):
    task_operation = self.__get_task_operation_name(message)  # First word
    task_name = self.__get_task_name(message)                  # Second word

    if task_operation == 'done':
        for task_name in self.__get_all_task_name(message):
            self.clean_task.update_task_updated_at(task_name)
        s3client.update_object(self.object_keyname, self.clean_task.get_json())
        return "Completed"

    if task_operation == 'add':
        duration = self.__get_duration(message)  # Third word
        self.clean_task.add_task(task_name, duration)
        # ...
```

### 5. Data Structure (JSON)

One JSON file per user is stored in S3.

```json
{
  "tasks": [
    {
      "task_name": "vacuum",
      "updated_at": "2024-01-01 12:00:00",
      "duration": 7,
      "paused": false
    }
  ],
  "notification": {
    "enabled": true,
    "days": ["Mon", "Wed", "Fri"],
    "hour": 7,
    "last_notified_at": "2024-01-01 07:00:00"
  }
}
```

## SAM Template

Infrastructure is defined using AWS SAM.

```yaml
# template.yaml
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31

Parameters:
  ChannelAccessToken:
    Type: String
    NoEcho: true
  BucketName:
    Type: String
    Default: your-bucket-name

Globals:
  Function:
    Runtime: python3.11
    Timeout: 300
    Environment:
      Variables:
        CHANNEL_ACCESS_TOKEN: !Ref ChannelAccessToken
        BUCKET_NAME: !Ref BucketName

Resources:
  # Message processing Lambda
  ProcessUserMessageFunction:
    Type: AWS::Serverless::Function
    Properties:
      Handler: line_clean_bot.process_user_message
      CodeUri: .
      Policies:
        - S3CrudPolicy:
            BucketName: !Ref BucketName
      Events:
        ApiEvent:
          Type: HttpApi
          Properties:
            Path: /process_user_message
            Method: ANY

  # Scheduled notification Lambda
  PushMessagePeriodicallyFunction:
    Type: AWS::Serverless::Function
    Properties:
      Handler: line_clean_bot.push_message_periodically
      CodeUri: .
      Policies:
        - S3CrudPolicy:
            BucketName: !Ref BucketName
      Events:
        ScheduleEvent:
          Type: Schedule
          Properties:
            Schedule: cron(0 * * * ? *)  # Every hour at minute 0
```

## Deployment

### 1. Prerequisites

- AWS CLI configured
- SAM CLI installed
- LINE Messaging API channel access token obtained

### 2. Build & Deploy

```bash
# Build
sam build

# Deploy
sam deploy --parameter-overrides ChannelAccessToken=your_token
```

### 3. Configure LINE Webhook

Set the API endpoint URL output after deployment as the Webhook URL in the LINE Developers console.

## Local Development

```bash
# Start local API server
sam local start-api --env-vars env.json

# Run tests
curl -X POST http://localhost:3000/process_user_message \
  -H "Content-Type: application/json" \
  -d @events/line_message.json
```

## Cost

Estimated monthly cost (assuming 100 users):

| Service | Cost |
|---------|------|
| Lambda | Nearly free (within free tier) |
| API Gateway | Nearly free (within free tier) |
| S3 | A few cents/month |
| **Total** | **A few cents to a few dollars/month** |

## Summary

- Serverless architecture minimizes operational costs
- S3 + JSON for simple data management
- SAM for infrastructure as code
- "Days elapsed" deadline management suited for cleaning tasks

Source code is available on [GitHub](https://github.com/bossagyu/line-clean-bot).

## Related Articles

- [How to Use the Cleaning Reminder Bot](/blog/045-clean-bot/)
- [LINE Messaging API Registration and Usage](/blog/002-line-messaging-api/)
