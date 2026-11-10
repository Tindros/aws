---
title: "Proposal"
date: 2026-09-30
weight: 2
chapter: false
pre: " <b> 2. </b> "
---


# Serverless Personal Expense Tracker
## AWS Serverless Solution for Personal Expense Management

### 1. Executive Summary
The Serverless Personal Expense Tracker is a web-based application designed to help users record, manage, review, and summarize their personal expenses through a serverless architecture on AWS.

The application provides core expense management features including user registration and login, adding expenses, viewing expense records, editing and deleting expenses, filtering expenses, and viewing a summary dashboard.

The web frontend is hosted by AWS Amplify. After the frontend files are delivered to the user's browser, the browser communicates directly with Amazon Cognito for authentication and directly with Amazon API Gateway for application API requests. Amazon Cognito provides authentication and issues JWT tokens that are included in API requests.

Amazon API Gateway is configured as an HTTP API with a JWT authorizer using the Amazon Cognito User Pool. Authenticated requests are forwarded to AWS Lambda, which contains the application business logic and performs read and write operations on Amazon DynamoDB.

Amazon DynamoDB stores expense records, while Amazon CloudWatch provides logging and monitoring for Lambda execution. An IAM Lambda Execution Role provides Lambda with the permissions required to access DynamoDB and CloudWatch following the principle of least privilege.

The serverless architecture reduces the need to manage traditional servers and provides a scalable and cost-efficient solution for a small-scale personal expense management application.


### 2. Problem Statement
### What’s the Problem?
Personal expense information is often recorded manually in notebooks, spreadsheets, or simple note-taking applications. These approaches can make it difficult to consistently organize expenses and quickly understand spending patterns.

Users may also need to calculate monthly spending, compare expenses by category, or search for transactions within a specific period. Performing these tasks manually can be time-consuming and may lead to inconsistent records or calculation errors.

In addition, a web-based expense management application requires an appropriate backend architecture for authentication, data storage, and API processing. Using a traditional continuously running server such as an EC2 instance may introduce unnecessary infrastructure management and costs for a small-scale personal application.


### The Solution
The project proposes a Serverless Personal Expense Tracker using AWS services.

Users access the web application through a browser. AWS Amplify hosts and deploys the frontend application, while the browser communicates directly with Amazon Cognito for user registration and login. After successful authentication, Cognito returns JWT tokens to the browser.

The browser sends authenticated expense requests to Amazon API Gateway using the JWT token in the Authorization header. API Gateway validates the JWT using a Cognito-based JWT authorizer and invokes AWS Lambda for valid requests.

AWS Lambda processes the expense management logic and performs create, read, update, and delete operations on Amazon DynamoDB. DynamoDB stores expense records associated with the authenticated user.

The application can also provide filtering and summary functions, allowing users to review total expenses and analyze spending by date or category.

An AWS IAM Lambda Execution Role provides Lambda with the required permissions to access DynamoDB and write logs to CloudWatch following the principle of least privilege. Amazon CloudWatch is used for logging and monitoring Lambda execution.


### Benefits and Return on Investment
The solution transforms personal expense tracking from a manual process into a web-based application that allows authenticated users to manage their expense records from a browser.

Using a serverless architecture reduces infrastructure management requirements because there is no need to maintain a continuously running application server. AWS Lambda executes application logic when API requests are received.

Amazon Cognito provides user authentication, while API Gateway and the JWT authorizer help protect application API routes so that expense data can be accessed only through authenticated requests.

The project also provides a foundation for future improvements, such as budget tracking, budget alerts, advanced analytics, expense visualization, recurring expenses, or additional notification services.

The expected operating cost is relatively low for a small-scale workload because the main AWS services use usage-based pricing. The final monthly and yearly cost will be estimated using the AWS Pricing Calculator based on the expected number of users, API requests, database operations, frontend usage, and monitoring activity.

### 3. Solution Architecture
The system uses an AWS Serverless architecture to provide authenticated personal expense management.

AWS Amplify hosts and deploys the frontend application. The user / web browser accesses the frontend through Amplify and runs the frontend code locally in the browser.

The browser communicates directly with Amazon Cognito for sign in and sign up. After successful authentication, Cognito returns a JWT token to the browser.

The browser sends API requests with the JWT token to Amazon API Gateway through an HTTP API. API Gateway uses a JWT authorizer configured with the Cognito User Pool to validate the token before invoking AWS Lambda.

AWS Lambda contains the expense service and business logic. It performs create, read, update, and delete operations on Amazon DynamoDB and can generate expense summaries for the authenticated user.

Amazon DynamoDB stores expense data. Amazon CloudWatch collects Lambda logs and metrics for monitoring, troubleshooting, and operational visibility.

The Lambda Execution Role provides Lambda with the required permissions to access DynamoDB and CloudWatch.

System Architecture

![Serverless Personal Expense Tracker Architecture](/aws/images/2-Proposal/architecture.jpeg)

### AWS Services Used
- **AWS Amplify**: Hosts and deploys the web frontend and serves the static frontend files.

- **Amazon Cognito**: Provides user registration, sign in, and authentication through a User Pool and issues JWT tokens.

- **Amazon API Gateway**: Provides the HTTP API, receives API requests from the user's browser, and uses a JWT authorizer to validate authenticated requests.

- **AWS Lambda**: Executes the expense management business logic and processes API requests.

- **Amazon DynamoDB**: Stores expense records and supports create, read, update, and delete operations.

- **AWS IAM**: Provides the Lambda Execution Role with the permissions required to access DynamoDB and CloudWatch.

- **Amazon CloudWatch**: Provides logging, metrics, and monitoring for Lambda execution.

### Component Design
- **Web Frontend**: AWS Amplify hosts the web application. The user's browser runs the frontend code after receiving the static files.

- **User Authentication**: Amazon Cognito User Pool handles user registration and login and returns JWT tokens to the browser.

- **API Layer**: Amazon API Gateway provides the HTTP API used by the frontend. A Cognito-based JWT authorizer validates the JWT included in the Authorization header.

- **Expense Service**: AWS Lambda contains the application business logic for creating, reading, updating, deleting, filtering, and summarizing expense records.

- **Data Storage**: Amazon DynamoDB stores expense records. Each record is associated with the authenticated user so that users can manage their own expense data.

- **Security**: Amazon Cognito authenticates users and API Gateway validates JWT tokens. An IAM Lambda Execution Role provides Lambda with the minimum required permissions to access DynamoDB and CloudWatch.

- **Monitoring**: Amazon CloudWatch collects Lambda logs and metrics for troubleshooting, monitoring, and operational review.

### Core Expense Data

The application can store expense records with fields such as:

- **userId**: Identifies the authenticated user who owns the expense record.

- **expenseId**: Unique identifier for the expense record.

- **amount**: Expense amount.

- **category**: Expense category such as Food, Transportation, Shopping, Bills, or Other.

- **description**: Optional description of the expense.

- **date**: Expense date.

- **createdAt**: Timestamp used to record when the expense was created.

### Core API Endpoints

The HTTP API can provide endpoints such as:

- **GET /expenses**: Retrieve expense records for the authenticated user.

- **POST /expenses**: Create a new expense record.

- **PUT /expenses/{id}**: Update an existing expense record.

- **DELETE /expenses/{id}**: Delete an expense record.

- **GET /summary**: Return expense summary information such as total spending and category-based totals.


### 4. Technical Implementation
**Implementation Phases**
The project focuses on building a complete serverless expense management application. The implementation can be divided into four phases:

- Requirements and architecture design: Define the expense management requirements, identify the required AWS services, finalize the architecture, and design the DynamoDB data structure and API endpoints.

- Authentication and backend development: Configure Amazon Cognito, create the API Gateway HTTP API with a JWT authorizer, implement AWS Lambda business logic, and configure DynamoDB operations.

- Frontend development and integration: Build the web interface, implement authentication flows, create expense management screens, connect the frontend directly to Cognito and API Gateway, and display expense and summary information.

- Testing, monitoring, optimization, and deployment: Perform end-to-end testing, configure IAM permissions and CloudWatch monitoring, handle errors, optimize the application, deploy the final frontend, and document the complete system.

**Technical Requirements**
- **Machine Learning:** Not required for the current MVP.

- **Database:** Amazon DynamoDB for storing expense records.

- **Backend:** AWS Lambda for processing expense management requests.

- **API:** Amazon API Gateway HTTP API with a JWT authorizer.

- **Authentication:** Amazon Cognito User Pool for user registration, login, and JWT-based authentication.

- **Frontend:** A web application that allows authenticated users to add, view, edit, delete, filter, and summarize expenses.

- **Deployment:** AWS Amplify for frontend hosting and deployment.

- **Security:** Cognito-based authentication, API Gateway JWT authorization, and an IAM Lambda Execution Role with minimum required permissions.

- **Monitoring:** Amazon CloudWatch Logs and Metrics.
### 5. Timeline & Milestones
**Project Timeline**
The project is developed throughout the **12-week internship**, combining AWS learning, project design, serverless implementation, frontend integration, testing, documentation, and final evaluation.

### Week 1 – AWS Fundamentals

- Create and configure an AWS account.
- Learn AWS cost management and AWS Support.
- Study AWS IAM and access management.
- Learn networking fundamentals with Amazon VPC.
- Study the basic AWS infrastructure and cloud service concepts.

**Milestone:** Complete the fundamental AWS learning modules and understand the AWS environment.

### Week 2 – AWS Compute, Storage & Database Services

- Learn IAM Roles for EC2.
- Study AWS Cloud9.
- Learn Amazon S3 and static website hosting.
- Study Amazon RDS.
- Learn AWS Lambda and serverless computing.
- Understand how AWS storage, database, and serverless services can support application development.

**Milestone:** Build foundational knowledge of the AWS services relevant to the project.

### Week 3 – Serverless Learning & Project Direction

- Continue studying AWS serverless and API-related services.
- Review AWS Lambda and API Gateway concepts.
- Review possible project directions.
- Define the requirements and core features of the Personal Expense Tracker.
- Confirm the serverless approach.

**Milestone:** Confirm the Serverless Personal Expense Tracker concept and the main AWS services.

### Week 4 – Project Requirements & Architecture Design

- Finalize the project requirements.
- Finalize the AWS architecture.
- Define the authentication flow using Amazon Cognito.
- Design the DynamoDB expense data structure.
- Define the API endpoints for expense management.
- Document the browser, authentication, API, backend, database, IAM, and monitoring flows.

**Milestone:** Complete the initial project architecture and technical design.

### Week 5 – Frontend Foundation

- Change the project direction from the initial House Price Prediction idea to the Serverless Personal Expense Tracker.
- Create the frontend using React, Vite, and TypeScript.
- Build the Login, Register, and Dashboard pages.
- Build the main expense management components.
- Create mock authentication and expense services for local development.
- Test the local add, view, edit, and delete workflow.

**Milestone:** Complete the basic frontend structure and local expense management workflow.

### Week 6 – Cognito, DynamoDB & IAM Foundation

- Configure the Amazon Cognito User Pool and App Client.
- Review the Cognito authentication flow and JWT tokens.
- Create the DynamoDB ExpenseTable.
- Design the partition key and sort key using userId and expenseId.
- Create the Lambda Execution Role.
- Configure the required permissions for DynamoDB and CloudWatch Logs.

**Milestone:** Complete the authentication, data storage, and IAM foundation for the backend.

### Week 7 – Lambda & API Gateway Backend

- Create the expenseTrackerFunction Lambda function.
- Implement GET, POST, GET by ID, PUT, DELETE, and summary operations.
- Connect Lambda to DynamoDB.
- Create the API Gateway HTTP API and Lambda integration.
- Configure expense routes and CORS.
- Configure the Cognito JWT Authorizer and protect the API routes.

**Milestone:** Complete the main authenticated serverless backend.

### Week 8 – Frontend & Backend Integration

- Integrate Amazon Cognito into the React frontend.
- Implement sign-in and token handling.
- Replace the mock expense service with the real API Gateway service.
- Send the Cognito JWT Access Token in the Authorization header.
- Connect the Dashboard to real expense and summary data.
- Test the complete add, view, edit, and delete workflow.

**Milestone:** Complete the frontend integration with the real AWS backend.

### Week 9 – System Testing & User Isolation

- Test Lambda GET, POST, PUT, DELETE, and summary operations.
- Test API Gateway JWT authentication and authorization.
- Verify valid and invalid authentication requests.
- Verify data isolation between users using the Cognito sub claim.
- Test unauthorized access and DELETE behavior for data outside the user's ownership.
- Review the Workshop documentation from 5.1 to 5.9.

**Milestone:** Verify the main security, authorization, and data-isolation behavior and stabilize the system.

### Week 10 – Workshop & Documentation Review

- Review the Workshop pages from 5.1 to 5.9.
- Verify that the documented AWS service configuration matches the actual project.
- Synchronize the Vietnamese and English Workshop pages.
- Update frontend API configuration references.
- Review the Amplify hosting instructions and frontend repository structure.
- Check image paths and prepare the screenshot and deployment checklist.

**Milestone:** Stabilize the Workshop documentation and align it with the actual project implementation.

### Week 11 – Deployment & Screenshot Verification

- Deploy the frontend using the configured hosting workflow.
- Verify authentication and expense management after deployment.
- Capture the required screenshots for Cognito, DynamoDB, Lambda, API Gateway, CloudWatch, and Amplify.
- Store the screenshots under the Workshop image directory.
- Check image paths and GitHub Pages rendering.
- Perform a final review of the Proposal, Workshop, and project configuration.

**Milestone:** Verify the deployed application and complete the required project evidence.

### Week 12 – Finalization & Evaluation Preparation

- Perform a final functional test of Cognito authentication, CRUD operations, and user data isolation.
- Finalize the Workshop screenshots and bilingual documentation.
- Review the Proposal, Worklog, Workshop, and repository structure.
- Perform AWS resource cleanup after verification where appropriate.
- Prepare the project and documentation for evaluation and report submission.

**Milestone:** Complete the Serverless Personal Expense Tracker documentation and prepare the repository for final evaluation.

## 6. Budget Estimate

The estimated cost can be viewed on the [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=d3feb73b825d0a1424aee2bcbb91b80078f5523d).

### Infrastructure Cost

- **Amazon Cognito:** 0.00 USD/month.
- **Amazon DynamoDB:** 0.00 USD/month.
- **AWS Lambda:** 0.00 USD/month.
- **Amazon API Gateway:** 0.00 USD/month.
- **Amazon CloudWatch:** 0.00 USD/month.
- **AWS Amplify:** 0.25 USD/month.

**Total:** **0.28 USD/month**, **3.36 USD/12 months**.

> The above cost is an estimate based on the configuration and expected usage of the project in the AWS Pricing Calculator. The actual cost may vary depending on resource usage, deployment region, AWS pricing policies, and any additional charges from AWS.

### Cost Optimization

To reduce operating costs, the project applies the following measures:

- **Using AWS Free Tier:** Cognito, DynamoDB, Lambda, API Gateway, and CloudWatch are configured with low usage levels suitable for a learning project and may remain within the Free Tier depending on actual usage.
- **AWS Lambda:** Uses a serverless model, so costs are based on the number of requests and execution time instead of maintaining continuously running servers.
- **Amazon DynamoDB:** Uses DynamoDB for expense data with a serverless model, which is suitable for the application's small data volume and request traffic.
- **Amazon API Gateway:** Uses an HTTP API instead of a REST API to reduce the cost of application requests.
- **AWS Amplify:** Used only to host the React frontend with the resources required for the project, avoiding unnecessary features or resources that could generate additional costs.
- **AWS CloudWatch:** Uses only the necessary logging and monitoring features to monitor Lambda and the system, avoiding unnecessary metrics or monitoring configurations.
- **Resource Cleanup:** After completing the testing and evaluation process, unused AWS resources will be deleted to avoid unexpected or unnecessary charges.

With the current configuration, the AWS Pricing Calculator estimates a cost of approximately **0.28 USD/month**, equivalent to **3.36 USD for 12 months**. The actual cost depends on resource usage and AWS pricing policies at the time of deployment.


### 7. Risk Assessment
#### Risk Matrix
- Unauthorized access to expense data: High impact, low probability.

- Lambda cannot access DynamoDB because of incorrect IAM permissions: High impact, low probability.

- API or frontend failure: Medium impact, low probability.

- Unexpected AWS cost increase: Medium impact, low probability.

- Data loss or incorrect expense records: High impact, low probability.

- Authentication or JWT configuration errors: High impact, low probability.

#### Mitigation Strategies
- Authentication and authorization: Use Amazon Cognito for user authentication and configure API Gateway JWT authorization for protected API routes.

- DynamoDB/Lambda access: Verify IAM permissions and ensure the Lambda Execution Role follows the principle of least privilege.

- API reliability: Test API Gateway with valid, invalid, missing, and unauthorized requests before deployment.

- Data integrity: Validate expense input and test create, update, and delete operations carefully.

- Cost control: Use AWS Budgets and monitor Lambda, DynamoDB, API Gateway, Cognito, CloudWatch, and Amplify usage.

- Monitoring: Review CloudWatch logs and metrics to identify application errors and operational issues.

#### Contingency Plans
If the deployed web application becomes unavailable, the project team can use the development environment to continue testing and debugging while restoring the deployed version.

If an application update produces unexpected behavior, the previous working frontend or Lambda version can be restored while the issue is corrected.

If database or authentication configuration causes problems during development, the affected component can be isolated and tested independently before re-integrating it into the complete application.

### 8. Expected Outcomes
#### Technical Improvements: 
The project is expected to transform personal expense tracking from a manual process into a Serverless Web Application that can be accessed through a web browser.

Users will be able to:

- Access the web application.

- Register and sign in securely.

- Add new expense records.

- View their expense history.

- Edit and delete expense records.

- Filter expenses by date or category.

- View total spending and category-based summaries.

Users will be able to manage their expenses without installing a dedicated desktop application or maintaining a personal backend server.
#### Long-term Value
The architecture is designed to be modular, allowing additional features to be added without requiring major changes to the overall system.

Future improvements may include:

- Budget management and budget alerts.

- Recurring expense support.

- More advanced analytics and visualizations.

- Additional expense categories and custom categories.

- Notifications using additional AWS services.

- Exporting expense data.

- More advanced monitoring and reporting.

The project also provides practical experience combining web development, AWS Cloud, authentication, database design, serverless computing, and monitoring, which is aligned with a Data Science specialization.
