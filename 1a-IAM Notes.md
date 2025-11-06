# 🧱 **AWS IAM & Security – Complete Developer Associate Notes**

---

## 🧭 1. AWS Global Infrastructure Overview

### 🌍 Core Concepts

AWS is designed to be globally distributed and fault-tolerant.

| Component                        | Description                                             | Example                       |
| -------------------------------- | ------------------------------------------------------- | ----------------------------- |
| **Region**                       | A geographical area containing multiple AZs.            | `ap-southeast-1` (Singapore)  |
| **Availability Zone (AZ)**       | One or more isolated data centers within a region.      | `ap-southeast-1a`, `1b`, `1c` |
| **Edge Location**                | Used by CloudFront for caching content closer to users. | CDN nodes                     |
| **Local Zone / Wavelength Zone** | For ultra-low-latency use cases near end users.         | Gaming, Telco apps            |

🔹 **Global Services** (not region-specific): IAM, Route53, CloudFront, WAF.
🔹 **Regional Services:** EC2, Lambda, DynamoDB, RDS, S3.

---

### 💡 Exam Tips

- If the question says “IAM region-specific?” → ❌ _It’s global_.
- If resource ARN doesn’t include region (e.g. `arn:aws:iam::123456789012:user/Dev`), it’s **global**.
- S3 bucket names are also **global**, even though data is regional.

---

### 🧩 **Sample Exam Question**

> Which AWS service is _global_ rather than regional?
> A. Amazon EC2
> B. Amazon S3
> C. AWS IAM
> D. AWS Lambda
> ✅ **Answer: C** – IAM is a global service.

---

## 🔐 2. IAM Core Concepts

IAM = Identity and Access Management.
It controls **who can do what on which resources**.

---

### 👥 IAM Entities

| Entity     | Purpose                                                                           |
| ---------- | --------------------------------------------------------------------------------- |
| **User**   | Represents a person or app that interacts with AWS directly.                      |
| **Group**  | Collection of users; policies can be attached at group level.                     |
| **Role**   | Temporary identity assumed by users or services (Lambda, EC2).                    |
| **Policy** | JSON document defining permissions (`Effect`, `Action`, `Resource`, `Condition`). |

---

### 🧠 IAM Policy Example

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:PutObject", "s3:GetObject"],
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

➡ “Allow uploading and reading any object in my-bucket.”

---

### 🔄 Policy Evaluation Logic

Order of evaluation when AWS checks permissions:

1. Explicit **Deny** (always wins)
2. Explicit **Allow**
3. Default **Deny**

💡 **Example Trap:**
User has `Allow s3:*` in group policy but inline user policy says `Deny s3:DeleteObject` → user **cannot delete** objects.

---

### 🧩 **Sample Exam Question**

> A user belongs to a group with `AmazonS3FullAccess`. You attach an inline policy to the user denying `s3:DeleteObject`.
> What happens?
> A. Group policy overrides user policy.
> B. Inline deny takes precedence, so delete fails.
> C. The user can delete since both are “Allow.”
> D. AWS merges both policies.
> ✅ **Answer: B**

---

## 🔑 3. IAM Roles & Trust Relationships

Roles are **assumed**, not directly logged into.
They provide **temporary credentials** through **AWS STS** (Security Token Service).

---

### 🔹 Trust Policy (who can assume the role)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": { "Service": "lambda.amazonaws.com" },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

### 🔹 Permission Policy (what can be done)

```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

💡 AWS exam loves to test this difference:
If your function has permission but fails with `AccessDenied`, the **trust policy** might be missing.

---

### 🧩 **Sample Exam Question**

> A Lambda function fails with `AccessDeniedException` when reading DynamoDB. The execution role has `AmazonDynamoDBReadOnlyAccess`.
> What’s wrong?
> A. Missing `s3:GetObject`
> B. No trust policy allowing Lambda service
> C. Lambda timeout
> D. IAM propagation delay
> ✅ **Answer: B**

---

## ⚙️ 4. Temporary Credentials & AWS STS

STS = **Security Token Service** → issues temporary access keys when assuming roles.

| Method                          | Description                                    | Default Duration   |
| ------------------------------- | ---------------------------------------------- | ------------------ |
| `sts:AssumeRole`                | Cross-account or role-based access             | 1 hour (up to 12h) |
| `sts:GetSessionToken`           | For MFA users                                  | 1 hour             |
| `sts:AssumeRoleWithWebIdentity` | For federated identity (e.g., Cognito, Google) | 1 hour             |

🧠 _Remember:_

- Duration you request (`--duration-seconds`) can’t exceed the **role’s `MaxSessionDuration`**.
- Temporary credentials = access key, secret, and session token.

---

### 🧩 **Sample Exam Question**

> A user runs `aws sts assume-role` requesting a 4-hour session, but credentials expire after 1 hour.
> Why?
> A. IAM role’s MaxSessionDuration is 1 hour.
> B. STS doesn’t support 4-hour sessions.
> C. Temporary tokens can’t exceed CLI limit.
> D. IAM user doesn’t have MFA.
> ✅ **Answer: A**

---

## 🧩 5. Cross-Account Access (High Frequency in Exams)

When an entity in **Account A** needs to access a resource in **Account B**, you need:

1. **IAM policy in Account A** – allows the action
2. **Resource policy / Trust policy in Account B** – allows the principal from A

📘 Example:

```json
// Account A (caller)
"Action": "sts:AssumeRole",
"Resource": "arn:aws:iam::222222222222:role/CrossAccountRole"

// Account B (target)
"Principal": { "AWS": "arn:aws:iam::111111111111:role/DevLambdaRole" },
"Action": "sts:AssumeRole"
```

💡 Always **two sides** of permission. If only one side allows → access denied.

---

### 🧩 **Sample Exam Question**

> A Lambda in Account A needs to read from S3 in Account B.
> What’s required?
> A. Add `AmazonS3ReadOnlyAccess` to Lambda role.
> B. Bucket policy in Account B allowing access from Lambda’s role ARN.
> C. Create an IAM user in Account B and share keys.
> D. Enable cross-region replication.
> ✅ **Answer: B**

---

## 🧰 6. Permission Boundaries, SCPs, and Conditions

### 🧩 Permission Boundary

Defines **maximum allowed actions**.
It **limits**, but never grants.

Example:

- User policy allows `s3:*`
- Boundary allows only `s3:Get*`, `s3:List*`
  ➡ User can’t delete or write.

---

### 🧩 Service Control Policy (SCP)

Organization-wide boundary applied via **AWS Organizations**.

- SCP doesn’t grant permissions.
- Even Admins are restricted by SCP denies.

---

### 🧩 Conditions (context-based controls)

Used in policies to add rules like IP, MFA, or time.

| Example         | Key                          | Usage                          |
| --------------- | ---------------------------- | ------------------------------ |
| Require MFA     | `aws:MultiFactorAuthPresent` | Bool                           |
| Restrict region | `aws:RequestedRegion`        | StringEquals                   |
| Time-based      | `aws:CurrentTime`            | DateGreaterThan / DateLessThan |
| Restrict IP     | `aws:SourceIp`               | CIDR range                     |

---

### 🧩 **Sample Exam Question**

> You want to enforce MFA before deleting S3 objects. Which condition applies?
> A. `"Bool": {"aws:MultiFactorAuthPresent": "true"}`
> B. `"StringEquals": {"aws:MFA": "enabled"}`
> C. `"BoolIfExists": {"aws:AuthType": "MFA"}`
> D. `"Bool": {"aws:SecureTransport": "true"}`
> ✅ **Answer: A**

---

## 📊 7. IAM Auditing Tools

| Tool                                       | Description                                    |
| ------------------------------------------ | ---------------------------------------------- |
| **CloudTrail**                             | Logs all API calls (who, when, from where)     |
| **Access Advisor**                         | Shows last used services per user/role         |
| **Credential Report**                      | Lists all IAM users, keys, MFA status          |
| **Access Analyzer**                        | Detects unintended public/cross-account access |
| **AWS Config Rule: `access-keys-rotated`** | Enforces rotation compliance                   |

---

### 🧩 **Sample Exam Question**

> Which tool identifies IAM users who haven’t used certain services recently?
> A. Access Advisor
> B. CloudTrail
> C. Credential Report
> D. Access Analyzer
> ✅ **Answer: A**

---

## 🧠 8. Shared Responsibility Model (Quick but Crucial)

| Responsibility                  | AWS                          | You |
| ------------------------------- | ---------------------------- | --- |
| Data Center Security            | ✅                           | ❌  |
| Patching EC2 OS                 | ❌                           | ✅  |
| IAM Role & Policy Configuration | ❌                           | ✅  |
| Disk Decommissioning            | ✅                           | ❌  |
| Encryption Key Management       | ✅ / ❌ (depends on KMS CMK) | ✅  |

💡 Trick: "Shared" means AWS secures the infrastructure; you secure configurations & access.

---

### 🧩 **Sample Exam Question**

> Who is responsible for configuring IAM policies securely?
> A. AWS
> B. Customer
> C. Shared between both
> D. AWS Security Team
> ✅ **Answer: B**

---

## 🧩 9. IAM Best Practices Summary

✅ **Do’s**

- Enable MFA for root and admins
- Follow _least privilege_ principle
- Use IAM roles, not access keys
- Rotate credentials regularly
- Audit using Access Advisor and CloudTrail

❌ **Don’ts**

- Hardcode credentials in code
- Use root account daily
- Attach `AdministratorAccess` to all roles
- Ignore CloudTrail logs

---

### ✅ **End-of-Section Quiz (Mini Mix)**

1. IAM is a **\_\_** service.
   A. Regional B. Global ✅ **Answer: B**
2. Who issues temporary credentials?
   A. IAM B. STS ✅ **Answer: B**
3. What happens if both Allow and Deny exist?
   ✅ **Deny wins**
4. Cross-account access fails when?
   ✅ Missing trust/resource policy on target
5. Where does EC2 store temporary credentials?
   ✅ Instance Metadata Service (`169.254.169.254`)
