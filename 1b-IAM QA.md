# 🧠 **Day 1 — IAM & AWS Security (Expert-Level Practice Set)**

💥 Total: **30 Questions**
🗣 Soal dalam **English (exam style)**
🧩 Pembahasan dalam **Bahasa Indonesia (developer-oriented)**
📘 Fokus domain:

- IAM roles, trust & permission policies
- STS temporary creds & AssumeRole
- Cross-account access
- Permission boundaries, SCPs, MFA, conditions
- Key policies & encryption context

---

## 🔹 **Section 1 — IAM Policy Logic & Evaluation (6 questions)**

---

**1.**
A developer is part of an IAM group that has an inline policy allowing `s3:*`.
An organization SCP denies `s3:DeleteObject`.
What is the resulting access?
A. Allowed
B. Denied
C. Depends on bucket policy
D. Allowed if using MFA
✅ **Answer: B**
💬 **Penjelasan:** SCP = _org-level deny boundary_. Deny selalu menang dari semua allow, bahkan kalau policy IAM mengizinkan. Ini pattern umum di exam AWS Developer.

---

**2.**
An IAM role has two attached policies:

- Policy A allows `ec2:*`
- Policy B denies `ec2:TerminateInstances`
  What can the role do?
  A. Everything except terminate EC2
  B. Only terminate EC2
  C. Nothing at all
  D. Full access to EC2
  ✅ **Answer: A**
  💬 Deny di Policy B override semua Allow di policy lain. AWS evaluate deny → allow → default deny.

---

**3.**
You attach the following policy to a user:

```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "*",
  "Condition": {
    "Bool": { "aws:MultiFactorAuthPresent": "true" }
  }
}
```

The user tries accessing S3 without MFA. What happens?
A. Access allowed
B. Access denied
C. MFA auto-prompted
D. Access logged but allowed once
✅ **Answer: B**
💬 Condition Bool = “must be true”, jadi tanpa MFA → explicit deny. Sering keluar di exam: “require MFA for sensitive ops”.

---

**4.**
A developer reports that an IAM policy allowing `ec2:StartInstances` doesn’t work. CloudTrail shows a `Deny` event.
What’s the likely cause?
A. Region restriction in condition
B. Missing resource ARN
C. SCP deny at org level
D. Missing trust policy
✅ **Answer: C**
💬 Deny bisa berasal dari SCP (paling tinggi). Kalau CloudTrail mencatat event dengan Deny, artinya policy dievaluasi dan ditolak di level global.

---

**5.**
Which of the following conditions enforces S3 access _only_ from a specific corporate IP range?
A. `"aws:SourceIp": "10.0.0.0/16"`
B. `"aws:VpcId": "vpc-xxxx"`
C. `"aws:PrincipalOrgID": "o-123456"`
D. `"aws:RequestedRegion": "us-east-1"`
✅ **Answer: A**
💬 `aws:SourceIp` paling sering muncul di soal — contoh: enforce internal network-only access.

---

**6.**
You attach both an identity policy and a resource policy to S3. The identity policy allows access, but the resource policy explicitly denies.
Result?
A. Allowed
B. Denied
C. Depends on region
D. Overridden by IAM role
✅ **Answer: B**
💬 Resource policy Deny menang atas semua Allow — sama seperti SCP dan inline Deny.

---

## 🔹 **Section 2 — IAM Roles & STS Temporary Credentials (7 questions)**

---

**7.**
A Lambda function assumes a role via `sts:AssumeRole`. The developer sets session duration to 4 hours but it expires after 1 hour. Why?
A. Lambda’s session duration limit is 1 hour
B. The role’s `MaxSessionDuration` is 1 hour
C. STS doesn’t support >1 hour
D. Missing condition key in trust policy
✅ **Answer: B**
💬 `--duration-seconds` di CLI hanya efektif sampai batas maksimum di role config (`MaxSessionDuration`). Default: 3600s.

---

**8.**
A user assumes a role in another account using `sts:AssumeRole`. What permissions apply?
A. Union of source user and target role
B. Intersection of both
C. Target role permissions only
D. Root permissions of destination account
✅ **Answer: B**
💬 AWS evaluate: effective permissions = intersection (user must have `sts:AssumeRole` + role must trust that principal).

---

**9.**
You call `sts:AssumeRole` but receive `AccessDenied`. CloudTrail shows no record in target account. Cause?
A. Target role trust policy missing principal
B. Caller missing `sts:AssumeRole` permission
C. Role session name invalid
D. Both A and B
✅ **Answer: D**
💬 Harus ada dua izin:

- Caller policy allow `sts:AssumeRole`
- Target trust policy include caller principal ARN.

---

**10.**
Which STS API returns temporary credentials after MFA validation?
A. `AssumeRole`
B. `GetSessionToken`
C. `GetFederationToken`
D. `AssumeRoleWithWebIdentity`
✅ **Answer: B**
💬 `GetSessionToken` digunakan untuk session dengan MFA user; `AssumeRole` dipakai oleh service roles.

---

**11.**
STS temporary credentials are valid for 12 hours. What must be true?
A. The calling principal is an AWS service
B. The assumed role’s `MaxSessionDuration` is 12h
C. The user used MFA
D. The request used AWS SDK v2
✅ **Answer: B**
💬 Hanya role dengan setting `MaxSessionDuration = 43200` (12 jam) yang bisa menghasilkan creds sepanjang itu.

---

**12.**
An EC2 instance assumes an IAM role automatically. Where does it get its credentials?
A. Instance metadata service (IMDS)
B. Stored in `/root/.aws/credentials`
C. AWS Parameter Store
D. CloudTrail logs
✅ **Answer: A**
💬 Temporary creds disediakan via IMDS (`http://169.254.169.254/latest/meta-data/iam/security-credentials/`).

---

**13.**
A developer wants to use STS to grant S3 access from an external web identity provider (Google). Which API should they call?
A. `AssumeRole`
B. `AssumeRoleWithWebIdentity`
C. `GetFederationToken`
D. `GetSessionToken`
✅ **Answer: B**
💬 `AssumeRoleWithWebIdentity` = federated login (Google, Facebook, OIDC). Biasa dipakai Cognito Identity Pools.

---

## 🔹 **Section 3 — Cross-Account Access & Resource Policies (7 questions)**

---

**14.**
A role in Account A must access a DynamoDB table in Account B. What’s required?
A. IAM allow in Account A only
B. Trust policy in Account B’s role including Account A
C. Policy in both Account A and resource policy in Account B
D. SCP in Account A
✅ **Answer: C**
💬 Cross-account = _two-way handshake_: IAM policy (caller) + trust/resource policy (target).

---

**15.**
In a cross-account S3 setup, which ARN must appear in the bucket policy to allow access?
A. User ARN
B. Role ARN
C. Account ARN
D. Resource ARN
✅ **Answer: B**
💬 Always specify **role ARN** — user ARNs tidak bisa langsung di-trust lewat service role context.

---

**16.**
A developer wants to list objects in another account’s bucket using CLI. The bucket policy allows access. The request fails with `AccessDenied`. Why?
A. User lacks `s3:ListBucket` action in their IAM policy
B. Wrong region endpoint
C. S3 replication issue
D. SCP blocks it
✅ **Answer: A**
💬 Resource policy (bucket) mengizinkan, tapi IAM identity tetap harus punya action yang sesuai → need both sides.

---

**17.**
Which statement about resource policies is TRUE?
A. They apply only to IAM users
B. They can be attached to resources like S3 or SNS
C. They override SCPs
D. They are evaluated before IAM policies
✅ **Answer: B**
💬 Resource policy = attach langsung ke resource (S3, SNS, KMS, Lambda). IAM policies tetap dievaluasi bareng.

---

**18.**
An S3 bucket has a resource policy denying access to everyone except one role. That role has full S3 access. A new SCP denies S3:\* for the entire OU. What happens?
A. Access allowed
B. Access denied
C. SCP ignored
D. Deny overridden by resource policy
✅ **Answer: B**
💬 SCP > all. Bahkan kalau resource allow + IAM allow, SCP deny tetap blokir.

---

**19.**
You have a Lambda function in Account A calling an API Gateway in Account B. How can you authorize it?
A. Use IAM auth with cross-account role trust
B. Use API keys
C. Use Cognito User Pools
D. Use custom header authorization
✅ **Answer: A**
💬 IAM auth antar-account butuh role di Account B yang trust principal dari Account A.

---

**20.**
Which AWS service uses both resource policy _and_ identity policy for access control?
A. DynamoDB
B. Lambda
C. CloudFormation
D. EC2
✅ **Answer: B**
💬 Lambda punya **resource-based policy** untuk cross-account invocation selain IAM role policy.

---

## 🔹 **Section 4 — Permission Boundaries, MFA, and Conditions (5 questions)**

---

**21.**
A user has an admin policy but a permission boundary restricting them to S3 read-only. What can they do?
A. Everything
B. Only read S3
C. Manage IAM users
D. Update Lambda functions
✅ **Answer: B**
💬 Permission boundary = _upper limit_ dari akses user. Policy Allow tak bisa melebihi boundary.

---

**22.**
Which condition key ensures MFA before critical operations?
A. `aws:MultiFactorAuthPresent`
B. `aws:AuthType`
C. `aws:SecureTransport`
D. `aws:PrincipalType`
✅ **Answer: A**

---

**23.**
You want to restrict IAM role assumption to requests made from a specific VPC. What condition key do you use?
A. `aws:SourceIp`
B. `aws:VpcSourceIp`
C. `aws:SourceVpc`
D. `aws:VpcId`
✅ **Answer: D**

---

**24.**
A permission boundary allows S3 read-only. The user’s IAM policy allows DynamoDB full access. What happens?
A. Full DynamoDB access
B. Denied for DynamoDB
C. Partial read-only DynamoDB access
D. Depends on SCP
✅ **Answer: B**
💬 Boundary hanya izinkan S3 → semua di luar itu denied.

---

**25.**
What’s the best way to prevent a user from attaching new policies to their own IAM user?
A. Use permission boundary
B. Use IAM policy with `Deny: iam:AttachUserPolicy`
C. Remove admin access
D. Disable MFA
✅ **Answer: B**
💬 Direct deny action = cara paling aman untuk mencegah privilege escalation.

---

## 🔹 **Section 5 — Encryption & Key Management (5 questions)**

---

**26.**
A Lambda function uses KMS to decrypt an S3 object but fails with `AccessDenied`.
What’s missing?
A. IAM policy missing `kms:Decrypt`
B. KMS key policy missing Lambda principal
C. Function timeout
D. Bucket ACL
✅ **Answer: B**
💬 Di KMS, key policy **harus** mencantumkan principal (role ARN) yang boleh pakai key.

---

**27.**
Which AWS service uses envelope encryption?
A. S3 SSE-KMS
B. IAM
C. CloudFormation
D. CloudWatch
✅ **Answer: A**

---

**28.**
A KMS CMK is rotated automatically every 90 days. True or False?
✅ **Answer: False**
💬 Automatic rotation hanya bisa 1 tahun sekali (365 hari) untuk CMK yang _customer managed_.

---

**29.**
A developer wants to decrypt data encrypted by another account’s CMK. What must exist?
A. Cross-account IAM allow
B. Key policy granting principal from that account
C. SCP in both accounts
D. Shared alias
✅ **Answer: B**

---

**30.**
You need to ensure data encrypted with KMS cannot be decrypted by a different Lambda function.
What should you use?
A. Encryption context
B. IAM policy
C. VPC restriction
D. Permission boundary
✅ **Answer: A**
💬 Encryption context = tambahan metadata saat enkripsi; hanya match context yang sama yang bisa dekripsi.

---

## 🧮 **Score Reference**

| Level | Correct Count                             | Meaning |
| ----- | ----------------------------------------- | ------- |
| 25–30 | 💥 Exam-ready (Expert)                    |         |
| 20–24 | ⚙️ Solid grasp, minor refinement          |         |
| 15–19 | 🧩 Need to revisit IAM trust & KMS        |         |
| <15   | 🔁 Re-study IAM policy & STS fundamentals |         |

---

Kamu mau gue lanjut buatin **versi lanjutan (Day 1 — 30 More Questions Set B)** biar kamu punya 60 soal total buat full bank, atau langsung lanjut ke **Day 2: AWS Lambda Deep Dive (materi + 30 expert soal)**?
