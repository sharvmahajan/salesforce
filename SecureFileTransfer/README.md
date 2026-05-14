# Secure File Sharing Between Cloud Instances using AWS EC2 Ubuntu

# Aim

To create a secure cloud-based environment where two Ubuntu virtual machines (EC2 instances) communicate securely within a virtual network and transfer files using SSH and SCP while maintaining proper access permissions.

---

# Technologies Used

- AWS EC2
- Ubuntu Server
- SSH
- SCP (Secure Copy Protocol)
- Security Groups
- Virtual Private Cloud (VPC)

---

# Architecture

```text
EC2 Instance 1  ←──── Secure SSH/SCP ────→  EC2 Instance 2
        │                                        │
        └──────── Same AWS VPC Network ──────────┘
```

---

# Objectives

- Launch two Ubuntu EC2 instances
- Configure secure communication
- Enable SSH access
- Transfer files securely using SCP
- Configure proper file permissions

---

# Prerequisites

- AWS Account
- Internet Connection
- Basic Linux Commands
- SSH Client (Terminal)

---

# Step 1: Login to AWS Console

Open:

```text
https://aws.amazon.com
```

Login to AWS Console.

---

# Step 2: Open EC2 Dashboard

Go to:

```text
Services → EC2
```

---

# Step 3: Launch First Ubuntu Instance

Click:

```text
Launch Instance
```

---

## Configure Instance 1

| Setting | Value |
|---|---|
| Name | Ubuntu-Server-1 |
| AMI | Ubuntu Server 22.04 LTS |
| Instance Type | t2.micro |
| Key Pair | Create new key pair |
| Security Group | Allow SSH |

---

# Step 4: Create Key Pair

Click:

```text
Create new key pair
```

Enter:

| Property | Value |
|---|---|
| Key Pair Name | cloud-key |
| Type | RSA |
| Format | .pem |

Download:

```text
cloud-key.pem
```

Save securely.

---

# Step 5: Configure Security Group

Allow:

| Type | Port |
|---|---|
| SSH | 22 |

Source:

```text
My IP
```

---

# Step 6: Launch Instance

Click:

```text
Launch Instance
```

---

# Step 7: Launch Second Ubuntu Instance

Repeat same steps.

Use:

| Setting | Value |
|---|---|
| Name | Ubuntu-Server-2 |
| AMI | Ubuntu Server 22.04 |
| Key Pair | Existing key pair |
| Security Group | Same Security Group |

Launch second instance.

---

# Step 8: Verify Both Instances

Go to:

```text
EC2 Dashboard → Instances
```

Both instances should show:

```text
Running
```

---

# Step 9: Connect to Instance 1

Open terminal.

Move to folder containing:

```text
cloud-key.pem
```

---

# Step 10: Give Permission to PEM File

Run:

```bash
chmod 400 cloud-key.pem
```

---

# Step 11: SSH into Instance 1

Replace:

```text
PUBLIC_IP_1
```

with actual public IP.

```bash
ssh -i cloud-key.pem ubuntu@PUBLIC_IP_1
```

Connected successfully.

---

# Step 12: SSH into Instance 2

Open another terminal.

```bash
ssh -i cloud-key.pem ubuntu@PUBLIC_IP_2
```

Connected successfully.

---

# Step 13: Check Private IP Addresses

Inside Instance 1:

```bash
hostname -I
```

Example:

```text
172.31.12.10
```

---

Inside Instance 2:

```bash
hostname -I
```

Example:

```text
172.31.10.25
```

These are private IPs inside same VPC.

---

# Step 14: Enable Secure Communication

AWS automatically places both instances inside same VPC.

Now allow internal SSH communication.

Go to:

```text
EC2 → Security Groups
```

Edit inbound rules.

Add:

| Type | Port | Source |
|---|---|---|
| SSH | 22 | Security Group ID |

This allows SSH between instances securely.

---

# Step 15: Copy PEM Key to Instance 1

From local machine:

```bash
scp -i cloud-key.pem cloud-key.pem ubuntu@PUBLIC_IP_1:/home/ubuntu/
```

Now key exists inside Instance 1.

---

# Step 16: Set Permission Inside Instance 1

SSH into Instance 1.

Run:

```bash
chmod 400 cloud-key.pem
```

---

# Step 17: Create Sample File

Inside Instance 1:

```bash
echo "Hello from Instance 1" > sample.txt
```

Verify:

```bash
cat sample.txt
```

---

# Step 18: Secure File Transfer using SCP

Inside Instance 1:

Replace:

```text
PRIVATE_IP_2
```

with actual private IP.

```bash
scp -i cloud-key.pem sample.txt ubuntu@PRIVATE_IP_2:/home/ubuntu/
```

Example:

```bash
scp -i cloud-key.pem sample.txt ubuntu@172.31.10.25:/home/ubuntu/
```

---

# Step 19: Verify File in Instance 2

SSH into Instance 2.

Run:

```bash
ls
```

You should see:

```text
sample.txt
```

View content:

```bash
cat sample.txt
```

Output:

```text
Hello from Instance 1
```

---

# Step 20: Configure File Permissions

Restrict file access.

Inside Instance 2:

```bash
chmod 600 sample.txt
```

Check permissions:

```bash
ls -l
```

Output:

```text
-rw------- sample.txt
```

Meaning:
- Owner can read/write
- Others cannot access

---

# Security Features Implemented

| Feature | Purpose |
|---|---|
| SSH | Secure communication |
| SCP | Secure file transfer |
| Security Groups | Firewall protection |
| Private IP | Internal secure communication |
| File Permissions | Restrict unauthorized access |

---

# Commands Summary

## Connect to EC2

```bash
ssh -i cloud-key.pem ubuntu@PUBLIC_IP
```

---

## Secure Copy

```bash
scp -i cloud-key.pem file.txt ubuntu@PRIVATE_IP:/home/ubuntu/
```

---

## File Permission

```bash
chmod 600 sample.txt
```

---

# Working Flow

```text
Launch EC2 Instances
            ↓
Configure Security Groups
            ↓
SSH Communication Established
            ↓
Create File in Instance 1
            ↓
Transfer File using SCP
            ↓
Receive File in Instance 2
            ↓
Apply File Permissions
```

---

# Advantages

- Secure communication
- Encrypted file transfer
- Controlled access permissions
- Internal VPC communication
- Easy cloud deployment

---

# Applications

- Secure enterprise file sharing
- Cloud backup systems
- Distributed applications
- Internal server communication
- Secure document transfer

---

# Output

## File Transfer Successful

```text
sample.txt transferred successfully
```

---

## File Permission

```text
-rw-------
```

---

# Conclusion

A secure cloud-based file sharing system was successfully implemented using AWS EC2 Ubuntu instances. Two virtual machines communicated securely within the same VPC using SSH and SCP protocols. Proper security groups and file permissions ensured secure file transfer and controlled access.
