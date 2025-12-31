# DevOps Learning Project – Movie Ticket Booking System

## Requirement Document (Console-First Implementation)

---

## 1. Project Objective

Build a **learning-focused, DevOps-centric Movie Ticket Booking System** on AWS using a **console-first approach**, followed by Infrastructure as Code (IaC) implementation in **TypeScript (AWS CDK)**.

The goal is not feature richness but **deep understanding of real-world DevOps concepts**, AWS service behavior, networking, security, cost control, and operational flows.

---

## 2. Target Audience

* Senior DevOps / Platform Engineer
* Solo project
* Learning-by-building approach

---

## 3. Core Constraints & Principles

### 3.1 Implementation Strategy

* **Phase 1**: Create ALL AWS resources manually via **AWS Console**
* **Phase 2**: Re-implement the same architecture using **AWS CDK (TypeScript)**
* No automated imports from console → IaC
* Console is treated as a **learning sandbox**, not the final state

### 3.2 Cost Control (Mandatory)

* Project must remain within **AWS Free Tier**
* No NAT Gateway
* No paid databases
* Resources must be **deleted after validation**
* Billing budget with alerts must be configured

---

## 4. Technology Stack

### 4.1 Cloud Platform

* AWS (single region only)

### 4.2 Compute

* Amazon ECS (Fargate)
* AWS Lambda (later phases)

### 4.3 Networking

* VPC
* Public & Private Subnets
* Internet Gateway
* Application Load Balancer (ALB)

### 4.4 Security

* Security Groups
* ACM (TLS certificates)
* HTTPS termination at ALB
* IAM roles (least privilege)

### 4.5 Application

* Dummy application
* Backend: Node.js (TypeScript)
* Frontend: Simple static or lightweight UI

---

## 5. Architecture Overview (High Level)

```
User Browser
  → TCP
  → TLS (HTTPS)
  → Application Load Balancer
  → Target Group
  → ECS Fargate Task
  → Application Response
```

TLS terminates at the ALB. Backend traffic remains HTTP.

---

## 6. Phase 1 – Console-Only Implementation Requirements

### 6.1 AWS Account Safety Setup

**Mandatory before any resource creation**:

* Create AWS Budget

  * Monthly limit: $5
  * Alert at $1
* Choose a **single AWS region** and never change it

---

### 6.2 Networking Requirements

Create via AWS Console:

* One VPC
* Two Public Subnets (multi-AZ)
* Two Private Subnets (multi-AZ)
* Internet Gateway
* Route Tables

Constraints:

* No NAT Gateway
* Only public subnets have IGW routes

Learning Focus:

* Route table behavior
* Subnet exposure
* Traffic flow understanding

---

### 6.3 Security Groups

Create the following security groups manually:

#### ALB Security Group

* Inbound: HTTPS (443) from 0.0.0.0/0
* Outbound: All

#### ECS Security Group

* Inbound: Application port (e.g. 3000) from ALB Security Group only
* Outbound: All

#### Database Security Group (Future)

* No inbound rules initially

Learning Focus:

* Trust boundaries
* Principle of least privilege

---

### 6.4 TLS & Certificates

* Use AWS Certificate Manager (ACM)
* DNS validation
* Attach certificate to ALB HTTPS listener

Key Concepts Covered:

* TLS handshake
* Certificate lifecycle
* HTTPS termination at ALB

---

### 6.5 Application Load Balancer

Create:

* Internet-facing ALB
* Public subnets
* HTTPS listener (443)
* Attach ACM certificate

Notes:

* Expect 503 until backend is attached
* Do not create target group initially

---

### 6.6 ECS (Minimal Setup)

Create:

* ECS Cluster (Fargate)
* Task Definition

  * Single container (nginx or simple Node app)
  * Exposed port
* ECS Service

  * Desired count: 1
  * Attach to ALB target group

Learning Focus:

* Task vs Service
* Target group registration
* Security group enforcement

---

### 6.7 Validation & Testing

Validate:

* ALB DNS works
* HTTPS certificate is valid
* Traffic reaches ECS task

Manually trace traffic flow end-to-end.

---

### 6.8 Cleanup (Mandatory)

After validation, delete:

* ECS Service
* ECS Cluster
* ALB
* Target Groups

Do not leave billable resources running.

---

## 7. Phase 2 – Infrastructure as Code (Future)

After console learning:

* Rebuild entire architecture using AWS CDK (TypeScript)
* Separate stacks:

  * NetworkStack
  * SecurityStack
  * ComputeStack
  * AppStack
* No console dependency

---

## 8. DevOps Topics Explicitly Covered

* TCP vs HTTP vs HTTPS
* TLS & certificates
* Load balancing
* Network isolation
* IAM & security groups
* Cost management
* Blue/green foundations (later)
* Observability (later)

---

## 9. Definition of Success

* Ability to explain **why** each AWS service is used
* Ability to trace request flow without diagrams
* Zero unexpected AWS charges
* Clean IaC recreation after console phase

---

## 10. Current Project Status

* Documentation: ✅ Completed
* NetworkStack (initial): ✅ Created
* Console-first build: 🔄 In Progress
