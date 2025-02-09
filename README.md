# Serverless Deployment with AWS Lambda
## Project Overview:
This project demonstrates how to deploy a serverless function using AWS Lambda and make it accessible via an HTTP endpoint using API Gateway.

Serverless computing removes the need for server management, allowing developers to focus on writing code. AWS Lambda is a popular platform for running serverless functions that are scalable and cost-efficient.

## Prerequisites:
- AWS account and credentials configured
- AWS CLI installed and configured
- Serverless Framework installed

## Setup:
##### 1. Install and Configure AWS CLI.
    
    aws configure

##### 2. Create a directory and change the directory.

    mkdir lambda-project
    cd lambda-project

## 🔹 Steps
### 1️⃣ Write a Simple Function (handler.py)
Create a Python function that returns a JSON response:

    import json

    def lambda_handler(event, context):
    response = {
        "statusCode": 200,
        "body": json.dumps({
            "message": "Hello from Lambda!"
        })
      }
      return response
      




### 2️⃣ Deploy as an AWS Lambda Function
##### 2.1. Create a ZIP package:
     
    zip function.zip handler.py
##### 2.2. Create Lambda Function using AWS CLI:

    aws lambda create-function \
      --function-name MyLambdaFunction \
      --runtime python3.8 \
      --role arn:aws:iam::<ACCOUNT_ID>:role/<ROLE_NAME> \
      --handler handler.lambda_handler \
      --zip-file fileb://function.zip
## 3️⃣ Configure API Gateway
3.1. Create REST-API

    aws apigateway create-rest-api --name 'MyLambdaAPI' --region us-east-1
3.2. Create a Resource

    aws apigateway create-resource \
      --rest-api-id <api-id> \
      --parent-id <parent-id> \
      --path-part hello

 - Replace <api-id> with the ID of the API you created in the previous step.
 - parent-id is the ID of the root resource.
    
3.3. Create a GET Method and Integration:
 
    aws apigateway put-method \
       --rest-api-id <api-id> \
       --resource-id <resource-id> \
       --http-method GET \
       --authorization-type NONE
       
3.4. Link Lambda to the API Gateway Method:

    aws apigateway put-integration \
       --rest-api-id <api-id> \
       --resource-id <resource-id> \
       --http-method GET \
       --integration-http-method POST \
       --type AWS_PROXY \
       --uri arn:aws:apigateway:us-east-1:lambda:path/ 2015-03-31/functions/arn:aws:lambda:us-east-1:your_account_id:function:myServerlessFunction/invocations

3.5. Grant API Gateway Permission to Invoke Lambda:

    aws lambda add-permission \
       --function-name myServerlessFunction \
       --principal apigateway.amazonaws.com \
       --statement-id <unique-statement-id> \
       --action "lambda:InvokeFunction" \
       --source-arn arn:aws:execute-api:us-east-1:your_account_id:<api-id>/*/GET/hello

## 4️⃣ Deploy the API

    aws apigateway create-deployment \
      --rest-api-id <api-id> \
      --stage-name prod

## 🚀 Testing the API
Once deployed, you can test the API using:

    curl -X GET https://<API_ID>.execute-api.<REGION>.amazonaws.com/dev/lambda

## 🔹 Outcome
A fully functional serverless API that scales automatically, accessible via an HTTP endpoint.
