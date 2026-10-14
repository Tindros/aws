---
title : "CloudWatch Monitoring & Amplify Hosting"
date : 2026-10-14 
weight : 8 
chapter : false
pre : " <b> 5.8. </b> "
---

#### Operational Observability & Global Frontend Delivery

Building a resilient serverless application requires both operational visibility into backend execution and automated, low-latency distribution of the web frontend.

+ **Amazon CloudWatch:** Serverless architectures rely on decentralized compute units. Amazon CloudWatch collects and centralizes execution logs, latency timings, and error traces from AWS Lambda, giving developers actionable diagnostic insights without SSH access or log shipping agents.
+ **AWS Amplify Hosting:** A fully managed web hosting service tailored for modern static frameworks (Vite, React, Vue, Next.js). Amplify delivers global CDN distribution powered by Amazon CloudFront, automated build workflows via `amplify.yml`, free managed SSL/TLS certificates, and branch-based deployment previews.

---

#### Section Modules

In this section, you will inspect Lambda execution traces in CloudWatch and publish the production React application using AWS Amplify Hosting:

1. [CloudWatch Logs & Metrics Observability](5.8.1-cloudwatch/)
2. [Deploy React Frontend to AWS Amplify Hosting](5.8.2-amplify-hosting/)
