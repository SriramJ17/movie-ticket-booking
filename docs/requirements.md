# 📄 Requirement Document

# DevOps‑Focused Movie Ticket Booking System

---

## 1. Purpose of This Document

This document defines the **functional, non‑functional, DevOps, security, and cost requirements** for a **learning‑focused movie ticket booking system**.

The goal is **not** to build a feature‑heavy application, but to:

* Practice **real‑world DevOps decision‑making**
* Understand **why specific AWS services are chosen**
* Learn **production‑style technical flows**
* Stay **within (or very close to) AWS Free Tier**

This project is built **from a Senior DevOps perspective**.

---

## 2. Project Scope

### In Scope

* Frontend + Backend APIs
* AWS managed services (no Kubernetes)
* CI/CD pipelines
* Infrastructure as Code
* Observability & SRE concepts
* Networking & Security fundamentals
* Cost‑aware architecture

### Out of Scope

* Real payment gateway integration
* Chaos engineering
* High‑availability multi‑region setup

---

## 3. Target Audience & Working Model

* **User**: Senior DevOps Engineer
* **Guidance Role**: Lead DevOps / Platform Engineer (architectural guidance, no full code)
* **Development Mode**: Solo project
* **Learning Focus**: High

---

## 4. Functional Requirements

### 4.1 User‑Facing Features

1. Browse movies and show timings
2. View seat availability
3. Select seats
4. Lock seats temporarily
5. Confirm booking (mock payment)
6. Receive booking confirmation

---

### 4.2 Backend Capabilities

* REST APIs for:

  * Movies
  * Shows
  * Seat availability
  * Booking
* Idempotent booking APIs
* Seat locking with expiry

---

## 5. Non‑Functional Requirements

### 5.1 Performance

* API latency (p95) < 300 ms (learning target)
* Seat lock operations must be atomic

### 5.2 Scalability

* Handle traffic spikes during popular releases
* Stateless services

### 5.3 Availability

* Target availability: 99.9% (conceptual SLO)

---

## 6. High‑Level Architecture

### 6.1 AWS Services Used

| Layer         | Service        | Reason for Choice                     |
| ------------- | -------------- | ------------------------------------- |
| Frontend      | S3             | Cheap static hosting                  |
| CDN           | CloudFront     | HTTPS, caching, global edge           |
| API           | ECS (Fargate)  | Long‑running APIs, deployment control |
| Async         | Lambda         | Event‑driven, free tier               |
| Messaging     | SQS            | Decoupling & retries                  |
| Database      | DynamoDB       | Conditional writes, TTL               |
| IaC           | AWS CDK        | Type‑safe infra, platform abstraction |
| CI/CD         | GitHub Actions | Native GitHub integration             |
| Observability | CloudWatch     | Free tier friendly                    |

---

## 7. Technical Flow (End‑to‑End)

### 7.1 Frontend Request Flow

1. User accesses application URL
2. DNS routes to CloudFront
3. CloudFront serves cached assets or fetches from S3
4. TLS terminates at CloudFront

---

### 7.2 API Request Flow

1. Browser sends HTTPS request
2. Request hits Application Load Balancer
3. ALB forwards traffic to ECS tasks
4. Container processes request
5. Response flows back through ALB

---

### 7.3 Booking Flow

1. Seat selection request
2. DynamoDB conditional write locks seat
3. Booking event sent to SQS
4. Lambda handles expiry / cleanup
5. Booking confirmation returned

---

## 8. DevOps Requirements

### 8.1 CI/CD

* GitHub Actions pipelines
* Stages:

  1. Lint & test (TypeScript)
  2. Build Docker image
  3. Push to ECR
  4. Deploy using CDK

---

### 8.2 Deployment Strategy

* Blue/Green deployments for ECS
* Zero‑downtime deployments

---

### 8.3 Infrastructure as Code

* AWS CDK (TypeScript)
* Separate stacks:

  * Network
  * Compute
  * Data
  * Observability

---

## 9. Observability & SRE Requirements

### 9.1 Metrics

* API latency (p95)
* Error rate
* Booking success rate

### 9.2 Logs

* Structured application logs
* Centralized in CloudWatch

### 9.3 SRE Concepts

* SLIs: latency, availability
* SLOs: conceptual only
* Error budgets: theoretical discussion

---

## 10. Security Requirements

### 10.1 Networking Fundamentals

* TCP 3‑way handshake understanding
* HTTP vs HTTPS
* HTTP/1.1 vs HTTP/2

---

### 10.2 TLS & Certificates

* AWS ACM managed certificates
* TLS termination at:

  * CloudFront
  * Application Load Balancer
* Understanding TLS handshake & cert chain

---

### 10.3 AWS Security

* IAM least privilege
* Task role vs execution role
* Secrets Manager for sensitive values
* Basic WAF rules

---

## 11. Cost Requirements (Critical)

### 11.1 Cost Philosophy

* Learning project must remain **free or near‑zero cost**
* Avoid always‑on services
* Infrastructure must be destroyed after use

---

### 11.2 Free‑Tier Strategy

| Service    | Cost Expectation    |
| ---------- | ------------------- |
| S3         | Free tier           |
| CloudFront | Near zero           |
| Lambda     | Free tier           |
| DynamoDB   | Free tier           |
| SQS        | Free tier           |
| ECS        | Minimal, time‑boxed |
| ALB        | Main cost risk      |

---

### 11.3 Cost Controls

* Deploy only during testing
* `cdk destroy` after use
* Avoid RDS initially
* Consider API Gateway + Lambda if needed

---

## 12. Final Deliverables

1. GitHub repository
2. Deployed demo URL (temporary)
3. Architecture diagrams
4. This requirement document

---

## 13. Living Document Note

This requirement document is **intentionally iterative**.
As learning progresses:

* Sections may be refined
* Architecture decisions may change
* Trade‑offs will be documented

This mirrors **real‑world engineering design evolution**.
