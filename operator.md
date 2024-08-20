## AWS Simple Queue Service (SQS) and EventBridge Subscription

AWS Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications. AWS EventBridge is a serverless event bus service that makes it easier to build event-driven applications by connecting application data from a variety of sources with your AWS services.

### Design Decisions

1. **Subscription Filtering**: Events are filtered to ensure only relevant events are sent to SQS, reducing unnecessary traffic and costs.
2. **Dead-Letter Queue (DLQ)**: A DLQ is set up to capture any messages that couldn't be processed successfully, preserving them for further analysis.
3. **Redrive Policy**: A redrive policy is in place to move messages to the DLQ after a certain number of processing attempts, allowing for error handling and analysis.
4. **Event Rules**: Defined rules to ensure that the right events are sent to the right SQS queues, improving efficiency and reducing noise.

## Runbook

### Unable to Receive Messages from SQS

If you are not receiving messages from SQS as expected, check the following:

```sh
aws sqs receive-message --queue-url "YOUR_SQS_QUEUE_URL"
```

**Expected Outcome**: You should see a list of message attributes if there are any messages in the queue. If the result is empty, ensure messages are being sent to the queue properly.

### EventBridge Rule Not Triggering

If an EventBridge rule isn't triggering, you can use the following commands to diagnose the issue:

```sh
aws events describe-rule --name "YOUR_RULE_NAME"
```

**Expected Outcome**: This should provide details about the rule, including its current state (ENABLED/DISABLED). Make sure the rule is enabled.

### Checking Dead-Letter Queue (DLQ) for Failed Messages

If messages are being moved to the DLQ, you can inspect them to diagnose why they failed:

```sh
aws sqs receive-message --queue-url "YOUR_DLQ_URL"
```

**Expected Outcome**: This should display the messages that were not processed successfully.

### Verifying Event Delivery Status

To ensure that events are being delivered from EventBridge to SQS:

```sh
aws events list-targets-by-rule --rule "YOUR_RULE_NAME"
```

**Expected Outcome**: You should see a list of targets for the rule. Verify that your SQS queue is listed as a target.

### Common SQL Queries for Debugging (if using a database for logging)

If you are logging events to a database and need to troubleshoot issues, here are some common SQL queries:

#### Checking for Recently Logged Events

```sql
SELECT * FROM event_logs ORDER BY created_at DESC LIMIT 10;
```

**Expected Outcome**: This should display the most recent events logged in your database.

#### Diagnosing Failed Event Logs

```sql
SELECT * FROM event_logs WHERE status = 'FAILED';
```

**Expected Outcome**: This will show events that have failed, providing insight into any errors that occurred.

### CloudWatch Logs for EventBridge

If you are leveraging CloudWatch Logs for better insights into EventBridge:

```sh
aws logs get-log-events --log-group-name "YOUR_LOG_GROUP_NAME" --log-stream-name "YOUR_LOG_STREAM_NAME"
```

**Expected Outcome**: Retrieve the logs that may contain error messages or other information useful for debugging.

### Redis Command for Checking Queue Length

If your setup involves Redis for queue management, check the length of your Redis queue:

```sh
redis-cli LLEN your_queue_name
```

**Expected Outcome**: Returns the number of elements in the specified queue, which can help you understand the current load and potential backlogs.

By following these troubleshooting steps and utilizing the respective commands, you should be able to manage and debug the AWS SQS and EventBridge subscriptions more effectively.

