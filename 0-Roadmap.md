**Roadmap** ini disusun langsung berdasarkan **AWS Certified Developer – Associate (DVA-C02) Official Exam Guide**.

---

# 🧭 **AWS Certified Developer – Associate (DVA-C02) Study Roadmap**

---

## 🧱 **DOMAIN 1 – Development with AWS Services (32%)**

💡 Fokus: Bangun dan deploy aplikasi pakai AWS SDK, Lambda, API Gateway, DynamoDB, dan layanan serverless lain.

| Study Day | Main Topics                                                           | Key Outcomes                                                                                | Example Exam Scenario                                                              |
| --------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| **Day 2** | AWS Lambda Fundamentals                                               | Understand invocation types, event sources, concurrency, permissions, cold start mitigation | _“A Lambda fails due to timeout when reading SQS messages — what’s the best fix?”_ |
| **Day 3** | API Gateway + Cognito                                                 | Build, secure, and throttle APIs; integrate Cognito auth; deploy multiple stages            | _“How do you add user authentication to API Gateway without managing passwords?”_  |
| **Day 4** | DynamoDB                                                              | Master partition keys, GSI/LSI, WCU/RCU, DAX caching, streams                               | _“A DynamoDB table is throttling — what’s the right scaling fix?”_                 |
| **Day 8** | Integration Services (SQS, SNS, Kinesis, EventBridge, Step Functions) | Understand async messaging, fan-out, retries, DLQs, and event chaining                      | _“When to use SNS vs SQS vs Kinesis for streaming data?”_                          |

🧠 **Goal:** Bisa bikin aplikasi serverless end-to-end yang reliable, scalable, dan event-driven.

---

## 🔐 **DOMAIN 2 – Security (26%)**

💡 Fokus: Terapkan prinsip _least privilege_, IAM roles, STS, Cognito, dan data encryption (KMS).

| Study Day | Main Topics                                                  | Key Outcomes                                                               | Example Exam Scenario                                                       |
| --------- | ------------------------------------------------------------ | -------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| **Day 1** | IAM, STS, Policies, Boundaries                               | Understand trust vs permission policy, STS temp creds, cross-account roles | _“A Lambda in Account A fails to access S3 in Account B — what’s missing?”_ |
| **Day 7** | Encryption & Secrets (KMS, Parameter Store, Secrets Manager) | Know encryption at rest/in transit, envelope encryption, key policies      | _“Where should you store DB credentials used by Lambda?”_                   |
| **Day 3** | Cognito                                                      | Handle authentication (User Pool) and authorization (Identity Pool)        | _“How to exchange Cognito token for temporary AWS credentials?”_            |

🧠 **Goal:** Kuasai _who can access what, from where, and how it’s secured._

---

## ⚙️ **DOMAIN 3 – Deployment (24%)**

💡 Fokus: Automasi deployment aplikasi dengan CI/CD pipeline dan Infrastructure as Code.

| Study Day | Main Topics                                    | Key Outcomes                                                             | Example Exam Scenario                                                                      |
| --------- | ---------------------------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------ |
| **Day 5** | CI/CD with CodePipeline, CodeBuild, CodeDeploy | Create pipelines, handle blue/green deploys, automate testing            | _“CodePipeline fails due to missing IAM permission — which service role must be updated?”_ |
| **Day 5** | Elastic Beanstalk + CloudFormation + SAM + CDK | Write IaC templates, manage stack lifecycle, rollbacks, and SAM policies | _“How to deploy serverless app with custom resources?”_                                    |

🧠 **Goal:** Bisa full automation — dari code commit sampai deploy ke AWS environment.

---

## 📈 **DOMAIN 4 – Troubleshooting & Optimization (18%)**

💡 Fokus: Logging, tracing, dan improving performance/cost pada aplikasi AWS.

| Study Day | Main Topics                   | Key Outcomes                                                           | Example Exam Scenario                                                            |
| --------- | ----------------------------- | ---------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| **Day 6** | CloudWatch, X-Ray, CloudTrail | Track metrics, traces, audit API calls                                 | _“Which service tracks request flow through multiple Lambda functions?”_         |
| **Day 9** | Optimization & Cost           | Lambda performance tuning, retries, backoff, caching (CloudFront, DAX) | _“Your Lambda function is running slow — what metric should you analyze first?”_ |

🧠 **Goal:** Bisa debug dan optimize aplikasi AWS secara efisien dan cost-aware.

---

## 💯 **Final Preparation**

| Study Day  | Focus                                 | Deliverable                     |
| ---------- | ------------------------------------- | ------------------------------- |
| **Day 10** | Full-length simulation (65 questions) | Practice exam + detailed review |
|            | Weak area recap                       | Revisit IAM, Lambda, DynamoDB   |
|            | Confidence pass check                 | Simulate under 130 min timer    |

---

## 🧩 **Extra Developer Add-ons (beyond exam scope but useful)**

- **EC2 Instance Roles** & metadata — included in exam indirectly
- **AppSync + Step Functions** — helps in advanced event-driven patterns
- **Copilot & ECS** — for containerized app deployment

---

## 🧾 **Study Approach**

- Each day = ~2–3 hours
- Mix between _concept digest (from me)_ + _exam-like practice (I’ll make)_
- No need to watch videos — I’ll handle all content & question simulations

---
