---
title: "Proposal"
date: 2026-09-30
weight: 2
chapter: false
pre: " <b> 2. </b> "
---


# Serverless House Price Prediction API
## AWS Serverless Solution for House Price Prediction

### 1. Executive Summary
The Serverless House Price Prediction API is a system designed to deploy a Machine Learning model as a serverless prediction service on AWS.

The prediction model is trained using Python and Scikit-learn on Google Colab with a housing dataset. After training, the model is exported as a .pkl or .joblib file and uploaded to Amazon S3 for storage.

When a user accesses the web application through AWS Amplify, the user's house features are submitted as an HTTP POST request to Amazon API Gateway. API Gateway invokes an AWS Lambda prediction function. Lambda retrieves the trained model from S3 and uses it to predict the house price.
    
The prediction result is then returned through API Gateway to the frontend and displayed to the user.

The serverless architecture reduces the need to manage traditional servers and provides a scalable and cost-efficient solution for a small-scale Machine Learning application.


### 2. Problem Statement
### What’s the Problem?
House price prediction models are often developed and tested in environments such as Google Colab or local computers. However, after training, the model does not provide a convenient way for users to submit house information and obtain predictions through a web application.

Running the model directly on a personal computer also makes it difficult to provide the prediction service to multiple users or integrate the model into a web application.

In addition, deploying a Machine Learning model as an API requires an appropriate backend architecture. Using a traditional continuously running server such as an EC2 instance may introduce unnecessary infrastructure management and costs for a small-scale prediction service.

### The Solution
The project proposes a Serverless House Price Prediction API using AWS services.

The Machine Learning model is trained outside AWS using Google Colab, Python, and Scikit-learn. After training and evaluation, the model is exported as a .pkl or .joblib file and uploaded to Amazon S3.

When a user accesses the web application hosted by AWS Amplify, the frontend sends the house features through an HTTP POST request to Amazon API Gateway. API Gateway invokes AWS Lambda, which retrieves the trained model from S3 and performs the prediction.

The predicted house price is returned through API Gateway to the frontend and displayed to the user.

An AWS IAM Execution Role provides Lambda with the required permissions to access the model stored in S3 following the principle of least privilege. Amazon CloudWatch is used for logging and monitoring Lambda execution.

### Benefits and Return on Investment
The solution transforms a Machine Learning model from a development environment into a web-based prediction service, allowing users to access the model without directly running Python or Google Colab.

Using a serverless architecture reduces infrastructure management requirements because there is no need to maintain a continuously running server. AWS Lambda executes the prediction function only when requests are received.

The project also provides a foundation for future improvements, such as using larger datasets, improving model accuracy, adding additional house features, or developing other Machine Learning APIs.

The expected operating cost is relatively low for a small-scale workload because the main AWS services use usage-based pricing. The final monthly and yearly cost will be estimated using the AWS Pricing Calculator based on the expected number of prediction requests and resource usage.

### 3. Solution Architecture
The system uses an AWS Serverless architecture to deploy the house price prediction model.

The Machine Learning model is trained outside AWS using Google Colab. A housing dataset is used during the training process. After training, the model is exported as a .pkl or .joblib file and uploaded to Amazon S3.

Within AWS, users access the web application hosted by AWS Amplify. The frontend sends house features to Amazon API Gateway through an HTTP POST request. API Gateway invokes AWS Lambda to process the prediction request.

Lambda retrieves the trained model from Amazon S3, performs the prediction, and returns the result through API Gateway to the frontend.

The Lambda Execution Role provides the required S3 permissions, while Amazon CloudWatch collects Lambda logs and metrics for monitoring.

System Architecture
![Serverless House Price Prediction API Architecture](/images/2-Proposal/architecture.jpeg)

### AWS Services Used
- **Amazon S3**: Stores the trained Machine Learning model (.pkl / .joblib).
- **AWS Lambda**: Executes the prediction function using the trained model.
- **Amazon API Gateway**: Provides the REST API and receives HTTP requests from the frontend.
- **AWS Amplify**: Hosts and deploys the web frontend.
- **AWS IAM**: Provides the Lambda Execution Role with the permissions required to access the model in S3.
- **Amazon CloudWatch**: Provides logging and monitoring for Lambda execution.

### Component Design
- **Model Training**: Google Colab, Python, and Scikit-learn are used to preprocess the housing dataset and train the regression model.
- **Model Storage**: The trained model is exported as a .pkl or .joblib file and uploaded to Amazon S3.
- **Web Frontend**: AWS Amplify hosts the web application where users enter house features.
- **API Layer**: Amazon API Gateway provides the REST API used to receive prediction requests.
- **Prediction Function**: AWS Lambda loads the trained model from S3 and performs the house price prediction.
- **Security**: An IAM Lambda Execution Role provides the required S3 permissions following the principle of least privilege.
- **Monitoring**: Amazon CloudWatch collects Lambda logs and metrics for troubleshooting and monitoring.


### 4. Technical Implementation
**Implementation Phases**
The project consists of two major parts: Machine Learning model development and AWS Serverless deployment. The implementation can be divided into four phases:

- Model research and development: Study the house price prediction problem, prepare the housing dataset, and develop a regression model using Python and Scikit-learn on - Google Colab.
- Model evaluation and deployment preparation: Evaluate the model using appropriate metrics, select the final model, and export it as a .pkl or .joblib file.
- Serverless API development: Upload the trained model to Amazon S3, implement the AWS Lambda prediction function, and configure Amazon API Gateway as the REST API endpoint.
- Frontend development, testing, and deployment: Build the web interface, connect the frontend to API Gateway, deploy the frontend using AWS Amplify, and perform end-to-end testing.

**Technical Requirements**
- Machine Learning: Python, Pandas, Scikit-learn, and other required libraries for data preprocessing, training, and evaluation.
- Model: A regression model exported as .pkl or .joblib.
- Storage: Amazon S3 for storing the trained model.
- Backend: AWS Lambda for processing prediction requests.
- API: Amazon API Gateway for providing the REST API.
- Frontend: A web application that allows users to enter house features and view the predicted house price.
- Deployment: AWS Amplify for hosting the frontend application.
- Security: IAM Lambda Execution Role with the minimum permissions required to access S3.
- Monitoring: Amazon CloudWatch Logs and Metrics.

### 5. Timeline & Milestones
**Project Timeline**
The project will be developed throughout the **12-week internship**, combining AWS learning, Machine Learning development, serverless implementation, testing, and documentation.

### Week 1 – AWS Fundamentals

- Create and configure an AWS account.
- Learn AWS cost management and AWS Support.
- Study AWS IAM and access management.
- Learn networking fundamentals with Amazon VPC.
- Study Amazon EC2 fundamentals.
- Understand the basic concepts of AWS infrastructure and cloud services.

**Milestone:** Complete the fundamental AWS learning modules and understand the basic AWS environment.

### Week 2 – AWS Compute, Storage & Database Services

- Learn IAM Roles for EC2.
- Study AWS Cloud9.
- Learn Amazon S3 and static website hosting.
- Study Amazon RDS.
- Learn AWS Lambda and serverless computing.
- Understand how AWS storage, database, and serverless services can be used in the project.

**Milestone:** Identify the AWS services required for the House Price Prediction project.

### Week 3 – Project Planning & Machine Learning Preparation

- Finalize the project requirements and system architecture.
- Prepare the housing dataset.
- Perform data cleaning and preprocessing.
- Explore the dataset and identify relevant house features.
- Establish a baseline regression model.
- Continue studying AWS services related to the project.

**Milestone:** Complete the initial Machine Learning pipeline and finalize the project architecture.

### Week 4 – Machine Learning Model Development

- Train regression models for house price prediction.
- Compare different model approaches.
- Evaluate model performance using appropriate metrics.
- Perform feature selection and preprocessing improvements.
- Select the initial model for deployment.

**Milestone:** Obtain a working House Price Prediction model with acceptable performance.

### Week 5 – Model Packaging & Amazon S3

- Export the trained model as .pkl or .joblib.
- Create and configure the Amazon S3 bucket.
- Upload the trained model to S3.
- Test downloading and loading the model from S3.
- Study S3 permissions and access control.

**Milestone:** Successfully store and retrieve the trained model from Amazon S3.

### Week 6 – AWS Lambda Prediction Function

- Develop the Lambda prediction function.
- Load the trained model from S3.
- Process input house features.
- Perform prediction using the trained model.
- Return the predicted house price.
- Test Lambda with sample input data.

**Milestone:** Complete a working serverless prediction function.

### Week 7 – API Gateway Integration

- Create an Amazon API Gateway REST API.
- Configure the HTTP POST endpoint.
- Connect API Gateway to AWS Lambda.
- Test requests and responses.
- Handle invalid or missing input data.

**Milestone:** Complete the backend prediction API.

### Week 8 – Frontend Development
- Design the web interface for house price prediction.
- Create input fields for house features.
- Implement frontend validation.
- Connect the frontend to API Gateway.
- Display the predicted house price.

**Milestone**: Complete a functional frontend connected to the prediction API.

### Week 9 – AWS Amplify Deployment
- Configure AWS Amplify for frontend hosting.
- Deploy the web application.
- Configure the frontend to communicate with the production API.
- Test the application through the deployed web interface.

**Milestone**: Deploy the first working version of the House Price Prediction web application.

### Week 10 – Security, Monitoring & Optimization
- Configure the Lambda Execution Role using IAM.
- Apply the principle of least privilege.
- Verify Lambda access to the S3 model.
- Configure and review Amazon CloudWatch logs.
- Monitor Lambda execution and errors.
- Optimize Lambda execution and model loading where possible.

**Milestone**: Complete the security and monitoring configuration.

### Week 11 – System Testing & Evaluation
- Perform end-to-end testing.
- Test different house feature combinations.
- Test invalid and incomplete input.
- Evaluate prediction accuracy.
- Identify and fix API, Lambda, S3, or frontend issues.
- Review AWS resource usage and estimated costs.

**Milestone**: Complete system testing and resolve major issues.

### Week 12 – Finalization & Documentation
- Finalize the Machine Learning model and AWS architecture.
- Review the complete system.
- Evaluate project results against the original objectives.
- Document the implementation process.
- Complete the internship Worklog and project documentation.
- Prepare the final project presentation and demonstration.

**Milestone**: Complete and present the Serverless House Price Prediction API.

### 6. Budget Estimation
The cost of the system depends on the number of prediction requests, Lambda execution time, model storage size in S3, API Gateway requests, frontend hosting, and data transfer.

The AWS Pricing Calculator will be used to estimate the expected monthly and yearly cost based on the actual project workload.

### Infrastructure Costs
- AWS Lambda: Cost depends on the number of prediction requests and Lambda execution time.
- Amazon S3: Cost depends on the storage size of the trained model and the number of requests used to access it.
- Amazon API Gateway: Cost depends on the number of API requests.
- AWS Amplify: Cost depends on frontend hosting, storage, and data transfer.
- Amazon CloudWatch: Cost depends on the amount of logs and metrics generated and retained.
- AWS IAM: IAM roles do not have a separate charge.

### Cost Optimization
Because the project is designed for a small-scale prediction workload, the expected number of requests is relatively low. The serverless architecture avoids the cost of maintaining an EC2 instance continuously when the system is not receiving requests.

Additional cost optimization can be achieved by:

- Keeping the trained model at an appropriate size.
- Optimizing Lambda execution time.
- Setting an appropriate CloudWatch log retention period.
- Monitoring S3 storage and API request usage.
- Using AWS Budgets to monitor and control spending.

The final monthly and annual cost will be calculated after defining the expected workload using the AWS Pricing Calculator.

### 7. Risk Assessment
#### Risk Matrix
- Low prediction accuracy: High impact, medium probability.
- Lambda cannot access the trained model in S3: High impact, low probability.
- API or frontend failure: Medium impact, low probability.
- Unexpected AWS cost increase: Medium impact, low probability.
- Unexpected model or data changes: High impact, low probability.

#### Mitigation Strategies
- Model accuracy: Evaluate the model using appropriate performance metrics and test it on data that was not used during training.
- S3/Lambda access: Verify IAM permissions and monitor Lambda logs to identify model access errors.
- API reliability: Test API Gateway with both valid and invalid requests before deployment.
- Cost control: Use AWS Budgets and monitor Lambda, S3, API Gateway, and Amplify usage.
- Model management: Keep previous model versions during development and verify a new model before deployment.

#### Contingency Plans
If the AWS prediction API becomes unavailable, the trained model can still be executed directly in the Python/Google Colab environment for prediction.

If a newly deployed model produces unexpected results, the previous model version can be restored and used until the new model is corrected.

### 8. Expected Outcomes
#### Technical Improvements: 
The project is expected to transform the House Price Prediction model from a Machine Learning development environment into a Serverless Web API that can be accessed through a web browser.

Users will be able to:

- Access the web application.
- Enter house features.
- Submit a prediction request.
- Receive the predicted house price directly through the web interface.

Users will not need to install Python or manually run Google Colab to use the prediction service.
#### Long-term Value
The architecture is designed to be modular, allowing the trained model to be replaced or updated without requiring major changes to the frontend or overall system.

Future improvements may include:

- Developing a more accurate Machine Learning model.
- Supporting multiple prediction models.
- Adding more housing features.
- Implementing model versioning.
- Adding user authentication.
- Improving monitoring and analytics.
- Expanding the system into other Machine Learning prediction APIs.

The project also provides practical experience combining Data Science, Machine Learning, AWS Cloud, and Serverless Architecture, which is aligned with a Data Science specialization.