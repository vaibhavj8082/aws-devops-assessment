# Deployment Steps

## Step 1 - Create Custom VPC

Created a custom VPC with CIDR block:

```text
10.0.0.0/16
```

The VPC provides isolated networking for the infrastructure.

---

## Step 2 - Create Public and Private Subnets

Created:
- Public Subnet 1 (10.0.1.0/24)
- Public Subnet 2 (10.0.2.0/24)
- Private Subnet 1 (10.0.3.0/24)
- Private Subnet 2 (10.0.4.0/24)

Subnets were distributed across multiple Availability Zones for high availability.

---

## Step 3 - Configure Internet Gateway

Created and attached an Internet Gateway to the VPC.

Configured public route table with:

```text
0.0.0.0/0 → Internet Gateway
```

This allows internet access for public subnets.

---

## Step 4 - Configure NAT Gateways

Created NAT Gateways inside public subnets.

Configured private route table with:

```text
0.0.0.0/0 → NAT Gateway
```

This allows outbound internet access for private EC2 instances while keeping them inaccessible from the internet.

---

## Step 5 - Configure Route Tables

Created:
- Public Route Table
- Private Route Table

Associated:
- Public subnets with public route table
- Private subnets with private route table

---

## Step 6 - Create Security Groups

Created:
- ALB Security Group
- EC2 Security Group

Security rules:
- ALB allows inbound HTTP traffic from internet
- EC2 allows HTTP traffic only from ALB Security Group
- Bastion Host temporarily allowed SSH access

---

## Step 7 - Launch Bastion Host

Launched a temporary Bastion Host (Test Instance) in Public Subnet 1.

Purpose:
- Securely access EC2 instances located in private subnets
- Avoid direct public SSH access to private EC2 instances

SSH access was temporarily enabled for administration.

---

## Step 8 - Launch Private EC2 Instances

Launched:
- nginx-server-1
- nginx-server-2

Both instances were deployed in private subnets across multiple Availability Zones.

---

## Step 9 - Access Private EC2 Through Bastion Host

Copied PEM key securely to Bastion Host.

Connected to private EC2 instances using SSH through the Bastion Host.

Example:

```bash
ssh -i key.pem ubuntu@<private-ip>
```

---

## Step 10 - Install and Configure Nginx

Installed Nginx on both EC2 instances.

Commands used:

```bash
sudo apt update
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

Configured custom HTML page displaying:
- Instance ID
- Availability Zone

---

## Step 11 - Remove SSH Access

After completing setup:
- SSH access was removed from security groups
- Bastion Host access was restricted

This improves security and follows least privilege principles.

---

## Step 12 - Create Target Group

Created Target Group for EC2 instances.

Configured health checks to monitor instance availability.

Registered both EC2 instances as targets.

---

## Step 13 - Create Application Load Balancer

Created internet-facing Application Load Balancer.

Configuration:
- Listener: HTTP Port 80
- Associated with public subnets
- Connected to target group

The ALB distributes incoming traffic across multiple EC2 instances.

---

## Step 14 - Testing and Validation

Verified:
- Target group health checks are healthy
- ALB DNS endpoint is accessible
- Traffic is distributed across multiple EC2 instances
- Security configuration works correctly

Successfully tested load balancing by refreshing webpage multiple times and observing different instance IDs.
