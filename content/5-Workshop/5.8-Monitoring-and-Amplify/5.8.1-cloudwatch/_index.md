---
title : "CloudWatch Logs & Metrics Observability"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.8.1 </b> "
---

Observability is essential for diagnosing issues, tracing API latencies, and understanding resource consumption in serverless systems. In this module, you will inspect runtime execution logs and review operational metrics using **Amazon CloudWatch**.

---

#### Step 1: Locate the Lambda Log Group

1. Open the [Amazon CloudWatch Console](https://us-east-1.console.aws.amazon.com/cloudwatch/home?region=us-east-1).
2. In the navigation pane, expand **Logs**, then select **Log groups**.
3. In the search box, enter `/aws/lambda/expenseTrackerFunction`:
   + This log group was automatically provisioned when the Lambda function made its initial log write.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.8-Monitoring-and-Amplify/cloudwatch-01-log-groups.png`  
*(To be captured during deployment: CloudWatch Log groups list showing /aws/lambda/expenseTrackerFunction)*
{{% /notice %}}
<!-- ![CloudWatch Log Groups](/images/5-Workshop/5.8-Monitoring-and-Amplify/cloudwatch-01-log-groups.png) -->

---

#### Step 2: Inspect Structured Request Logs

1. Click on `/aws/lambda/expenseTrackerFunction`.
2. Under the **Log streams** tab, click the topmost (most recent) log stream.
3. Observe the structured log events generated during API interactions:
   + **Incoming Request:** Displays the `routeKey`, `httpMethod`, and decoded Cognito `sub` identifier.
   + **Execution Trace:** Shows DynamoDB command execution (`QueryCommand`, `PutCommand`).
   + **Standard AWS REPORT Line:** Shows performance and billing metrics for each invocation:
     ```
     REPORT RequestId: 4f1a2b3c-... Duration: 42.15 ms Billed Duration: 43 ms Memory Size: 128 MB Max Memory Used: 78 MB
     ```

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.8-Monitoring-and-Amplify/cloudwatch-02-log-stream-event.png`  
*(To be captured during deployment: Detailed CloudWatch log stream showing incoming request context and REPORT line)*
{{% /notice %}}
<!-- ![Log Stream Events](/images/5-Workshop/5.8-Monitoring-and-Amplify/cloudwatch-02-log-stream-event.png) -->

---

#### Step 3: Review Lambda Operational Metrics

1. In the CloudWatch navigation pane, choose **Metrics**, then select **All metrics**.
2. Select **Lambda** -> **By Function Name**.
3. Locate `expenseTrackerFunction` and select the following metrics:
   + **Invocations:** Aggregates total API request volume.
   + **Duration:** Measures execution time in milliseconds. Notice how subsequent warm invocations drop to under 30ms.
   + **Errors:** Displays unhandled execution exceptions (should remain zero).
   + **Throttles:** Indicates if concurrent execution limits were reached.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.8-Monitoring-and-Amplify/cloudwatch-03-lambda-metrics.png`  
*(To be captured during deployment: CloudWatch Metrics graphs displaying Invocations, Duration, and Errors)*
{{% /notice %}}
<!-- ![CloudWatch Lambda Metrics](/images/5-Workshop/5.8-Monitoring-and-Amplify/cloudwatch-03-lambda-metrics.png) -->

{{% notice tip %}}
**Operational Insight:** Because DynamoDB and Lambda scale horizontally on demand, execution times remain stable even under simultaneous user requests.
{{% /notice %}}
