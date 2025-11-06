# 🧠 **AWS Lambda – Expert Practice Questions (DVA-C02 Edition)**

---

### **Q1.**

A Lambda function needs to process uploaded images from an S3 bucket. Occasionally, some events are missing. What’s the MOST likely reason?

A. Lambda throttling
B. S3 Event Notification not configured correctly
C. S3 delivers events at-least-once, and some are overwritten
D. Lambda timeout is too short

✅ **Answer:** C
💬 **Explanation:** S3 delivers notifications _at-least-once_, meaning duplicates or overwrites can occur. Functions must be idempotent.

---

### **Q2.**

A company wants to ensure their Lambda function maintains consistent response time during peak load. What should they do?

A. Increase function memory
B. Configure Provisioned Concurrency
C. Add reserved concurrency
D. Use multi-AZ Lambda deployments

✅ **Answer:** B
💬 **Explanation:** Provisioned Concurrency pre-initializes environments, removing cold starts → consistent latency under heavy load.

---

### **Q3.**

A developer frequently updates Lambda environment variables and notices longer cold starts. Why?

A. Environment variables reset memory
B. Changing environment variables forces environment reinitialization
C. Too many aliases
D. Memory allocation mismatch

✅ **Answer:** B
💬 **Explanation:** Updating environment variables triggers container rebuild → new environment → cold start.

---

### **Q4.**

A function that processes DynamoDB Streams shows increasing `IteratorAge`. What’s happening?

A. Lambda retries are failing
B. Function is being throttled
C. Shards are deleted
D. DynamoDB table has TTL expired

✅ **Answer:** B
💬 **Explanation:** `IteratorAge` increases when Lambda can’t keep up → usually due to throttling or slow processing.

---

### **Q5.**

Which invocation type allows up to two automatic retries with exponential backoff?

A. Synchronous
B. Asynchronous
C. Stream-based
D. EventBridge

✅ **Answer:** B
💬 **Explanation:** Async invokes (S3, SNS, EventBridge) retry twice: 1 min + 2 min delay.

---

### **Q6.**

A Lambda connected to an RDS instance frequently runs out of connections. What’s the BEST solution?

A. Increase RDS capacity
B. Reuse DB connections outside the handler
C. Use VPC Endpoint
D. Add Provisioned Concurrency

✅ **Answer:** B
💬 **Explanation:** Connection pooling outside handler allows reuse between warm invocations → fewer open connections.

---

### **Q7.**

Which AWS service should be used to gradually deploy new Lambda versions?

A. CodePipeline
B. CodeDeploy
C. CodeBuild
D. CloudFormation

✅ **Answer:** B
💬 **Explanation:** CodeDeploy + Lambda aliases enables linear or canary traffic shifting.

---

### **Q8.**

A developer needs to store API credentials securely and rotate them automatically. Which service is best?

A. SSM Parameter Store (plain-text)
B. Environment variables
C. AWS Secrets Manager
D. KMS Key directly

✅ **Answer:** C
💬 **Explanation:** Secrets Manager handles encrypted secrets and rotation integration automatically.

---

### **Q9.**

A Lambda function cannot connect to the internet when placed inside a private VPC subnet. Why?

A. No NAT Gateway configured
B. Function not attached to Security Group
C. Missing VPC endpoint for S3
D. DNS resolution disabled

✅ **Answer:** A
💬 **Explanation:** Private subnets need a NAT Gateway for outbound internet access.

---

### **Q10.**

Which of the following statements about Lambda Layers is TRUE?

A. Layers are mutable after upload
B. Layers can contain up to 10 GB of data
C. A function can use up to 5 layers
D. Layers replace environment variables

✅ **Answer:** C
💬 **Explanation:** Each Lambda can include a maximum of 5 layers. Layers are versioned and immutable.

---

### **Q11.**

A developer observes occasional duplicate messages when processing from an SQS queue. What’s the correct handling?

A. Use FIFO queue
B. Enable retries
C. Make function idempotent
D. Increase timeout

✅ **Answer:** C
💬 **Explanation:** SQS guarantees _at-least-once delivery_, so idempotency ensures duplicates don’t break processing.

---

### **Q12.**

Which metric indicates backlog for stream-based Lambda invocations?

A. Duration
B. Throttles
C. IteratorAge
D. Concurrency

✅ **Answer:** C
💬 **Explanation:** `IteratorAge` measures delay between record creation and Lambda processing — backlog indicator.

---

### **Q13.**

A Lambda function’s CloudWatch logs show no output. What’s the most probable cause?

A. Missing CloudWatch agent
B. Missing execution role permission
C. Function timeout
D. Log group deleted

✅ **Answer:** B
💬 **Explanation:** The IAM execution role must include permission `logs:CreateLogStream` and `logs:PutLogEvents`.

---

### **Q14.**

You want to capture detailed latency information across Lambda, DynamoDB, and API Gateway. What should you use?

A. CloudWatch Alarms
B. CloudTrail
C. AWS X-Ray
D. EventBridge

✅ **Answer:** C
💬 **Explanation:** AWS X-Ray provides end-to-end tracing and latency visualization across AWS services.

---

### **Q15.**

What’s the MAX timeout value for AWS Lambda?

A. 5 minutes
B. 10 minutes
C. 15 minutes
D. 30 minutes

✅ **Answer:** C
💬 **Explanation:** Lambda allows up to 900 seconds (15 minutes) per invocation.

---

### **Q16.**

A Lambda using the Node.js runtime has inconsistent latency due to large dependencies. What’s the best optimization?

A. Split function logic
B. Bundle dependencies and use Lambda Layers
C. Switch to Python runtime
D. Use bigger timeout

✅ **Answer:** B
💬 **Explanation:** Lambda Layers reduce cold start by offloading heavy dependencies.

---

### **Q17.**

A Lambda in Account A must be triggered by an EventBridge rule from Account B. What’s required?

A. Resource-based policy on Lambda
B. IAM role with trust to Account B
C. Add Lambda to the same VPC
D. S3 notification rule

✅ **Answer:** A
💬 **Explanation:** Cross-account invocation requires resource-based policy on Lambda.

---

### **Q18.**

A Lambda execution role allows S3 read access, but the function still fails to access the bucket. Why?

A. Missing trust policy
B. Bucket policy denies Lambda access
C. Function in private VPC
D. Wrong KMS key

✅ **Answer:** B
💬 **Explanation:** Even if Lambda has permission, the bucket’s policy can explicitly deny access.

---

### **Q19.**

You need to deploy Lambda with Infrastructure as Code using TypeScript. What should you use?

A. AWS SAM
B. AWS CDK
C. CloudFormation template
D. CodeDeploy

✅ **Answer:** B
💬 **Explanation:** AWS CDK supports IaC using modern programming languages like TypeScript and Python.

---

### **Q20.**

Which Lambda concurrency type eliminates cold starts entirely?

A. Reserved concurrency
B. Provisioned concurrency
C. Default concurrency
D. Shared concurrency

✅ **Answer:** B
💬 **Explanation:** Provisioned Concurrency keeps environments warm and ready → no cold start latency.

---

### **Q21.**

A Lambda triggered by S3 is failing silently. What’s missing?

A. DLQ configuration
B. CloudWatch Alarm
C. Retry policy
D. Resource-based policy

✅ **Answer:** A
💬 **Explanation:** Async invokes (like S3) drop failed events if DLQ or Destination isn’t configured.

---

### **Q22.**

A function processes millions of events daily and costs are increasing. How to reduce cost efficiently?

A. Reduce timeout
B. Lower memory allocation
C. Use ARM/Graviton runtime
D. Decrease concurrency

✅ **Answer:** C
💬 **Explanation:** Graviton runtime provides ~20–30% lower cost with same performance.

---

### **Q23.**

Which AWS service provides a retry workflow and error handling between multiple Lambdas?

A. Step Functions
B. CodePipeline
C. EventBridge
D. X-Ray

✅ **Answer:** A
💬 **Explanation:** Step Functions orchestrate retries, error handling, and parallel workflows.

---

### **Q24.**

A function must securely access a private RDS instance. What’s required?

A. Attach Lambda to private subnet with security group
B. NAT Gateway in private subnet
C. Public IP on Lambda
D. RDS IAM role

✅ **Answer:** A
💬 **Explanation:** Lambda must be in the same VPC/subnet and have an appropriate SG to connect to private RDS.

---

### **Q25.**

You want to automatically retry failed async invocations and route failures to EventBridge. Which feature supports this?

A. DLQ
B. Destinations
C. CloudWatch Logs
D. Step Functions

✅ **Answer:** B
💬 **Explanation:** Lambda Destinations can send success/failure events to SNS/SQS/EventBridge.

---

### **Q26.**

A developer wants to test a Lambda locally before deployment. Which tool helps?

A. AWS CLI
B. SAM CLI
C. CloudFormation
D. CDK Synth

✅ **Answer:** B
💬 **Explanation:** AWS SAM CLI allows `sam local invoke` for local testing using Docker.

---

### **Q27.**

Lambda must invoke another function asynchronously with guaranteed delivery. What’s the best approach?

A. Direct invoke with SDK
B. Use EventBridge as intermediary
C. Use API Gateway
D. Invoke via Step Functions

✅ **Answer:** B
💬 **Explanation:** EventBridge ensures at-least-once delivery with retry and DLQ support.

---

### **Q28.**

A function deployed via CodeDeploy failed halfway through rollout. What happens?

A. Rollback triggered automatically
B. Alias deleted
C. Function disabled
D. Must redeploy manually

✅ **Answer:** A
💬 **Explanation:** CodeDeploy automatically rolls back when CloudWatch alarms fail during deployment.

---

### **Q29.**

Which AWS metric shows how long a function runs?

A. Duration
B. Throttles
C. IteratorAge
D. Concurrency

✅ **Answer:** A
💬 **Explanation:** `Duration` measures function execution time in milliseconds.

---

### **Q30.**

Which combination represents a fully serverless web app stack?

A. API Gateway + Lambda + DynamoDB + S3
B. EC2 + S3 + RDS
C. ECS + CloudFront + RDS
D. Lambda + CloudFormation + RDS

✅ **Answer:** A
💬 **Explanation:** API Gateway (API layer), Lambda (compute), DynamoDB (database), S3 (static hosting) → classic serverless architecture.

---

## 📊 **Score Guide (for practice use)**

| Score Range | Skill Level     | Recommendation                               |
| ----------- | --------------- | -------------------------------------------- |
| 27–30       | 🏆 Expert       | Ready for exam                               |
| 22–26       | 💪 Strong       | Revise IAM & VPC sections                    |
| 18–21       | ⚙️ Intermediate | Focus on DLQ, concurrency, and tracing       |
| <18         | 🧩 Foundation   | Revisit Lambda fundamentals & retry behavior |

---

Kalau kamu mau, gue bisa generate semua ini dalam bentuk **Markdown file lengkap (`Lambda-Exam-Questions.md`)**
dengan syntax highlighting, collapsible “Answer + Explanation” sections (pakai `<details>` tags biar interaktif di GitHub).

Bikin sekalian format GitHub-friendly itu?
