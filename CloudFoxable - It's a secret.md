# CloudFoxable---It-s-a-secret
In this challenge, we were given an AWS user with specific IAM policies and Our goal was to use CloudFox to find and retrieve a hidden secret named its-a-secret.

# CloudFoxable 3 - It's a Secret

## 📝 Challenge Description  
In this challenge, we were given an AWS user with specific IAM policies:
- **SecurityAudit** (AWS Managed)
- **CloudFox** (Customer Managed)
- **its-a-secret** (Customer Managed)

Our goal was to **use CloudFox to find and retrieve a hidden secret** named `its-a-secret`.

---

## Step 1: Verify AWS Profile Setup  
To ensure we were using the correct AWS profile, we ran:

```sh
aws --profile cloudfoxable sts get-caller-identity
```

This confirmed our **AWS Account ID** and user identity.

Output:
```json
{
    "UserId": "AID...",
    "Account": "739275443872",
    "Arn": "arn:aws:iam::739275443872:user/Admin"
}
```

Since our AWS identity was correctly set up, we proceeded to run CloudFox.

---

## Step 2: Run CloudFox to Enumerate Secrets  
CloudFox is an AWS enumeration tool that helps identify interesting resources.  
To find available secrets, we ran:

```sh
cloudfox aws --profile cloudfoxable secrets
```

### **CloudFox Output:**
```plaintext
╭──────────────────────────────────────────────────────────╮
│    Service     │  Region   │                 Name                  │           Description            │
├──────────────────────────────────────────────────────────├
│ SecretsManager │ us-west-2 │ DomainAdministrator-Credentials       │                                  │
│ SecretsManager │ us-west-2 │ database-secret                       │                                  │
│ SSM            │ us-west-2 │ /cloudfoxable/flag/its-a-secret       │                                  │
╯──────────────────────────────────────────────────────────╯
```

This revealed that **`its-a-secret` was stored in AWS Systems Manager (SSM) Parameter Store**, not in Secrets Manager.

---

## Step 3: Retrieve the Secret from SSM Parameter Store  
Since `its-a-secret` was found in SSM, we attempted to retrieve it:

```sh
aws --profile cloudfoxable ssm get-parameter --name "/cloudfoxable/flag/its-a-secret" --with-decryption
```

### **Success! 🎉**
```json
{
    "Parameter": {
        "Name": "/cloudfoxable/flag/its-a-secret",
        "Type": "SecureString",
        "Value": "FLAG{your_ctf_flag_here}"
    }
}
```

This revealed the **flag** needed to complete the challenge.

---

## Lessons Learned  
1. **CloudFox is powerful for AWS reconnaissance** – It quickly identified secrets across **Secrets Manager** and **SSM Parameter Store**.
2. **Secrets aren’t always in Secrets Manager** – This challenge highlighted the importance of checking both **Secrets Manager** and **SSM Parameter Store**.
3. **AWS CLI can retrieve secrets with proper permissions** – `aws ssm get-parameter --with-decryption` allows us to decrypt and view secure values.

---

## Summary of Commands Used  
```sh
# Verify AWS identity
aws --profile cloudfoxable sts get-caller-identity

# Run CloudFox to enumerate secrets
cloudfox aws --profile cloudfoxable secrets

# Retrieve the secret from SSM
aws --profile cloudfoxable ssm get-parameter --name "/cloudfoxable/flag/its-a-secret" --with-decryption
```

---

## 📌 Conclusion  
This challenge demonstrated **AWS enumeration using CloudFox** and **retrieving hidden secrets** from SSM Parameter Store. By methodically searching with CloudFox and leveraging AWS CLI, we successfully retrieved the flag.

---

