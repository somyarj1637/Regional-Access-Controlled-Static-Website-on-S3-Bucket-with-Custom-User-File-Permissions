# ****Regional Access-Controlled Static Website on S3 Bucket with Custom User File Permissions****

# **Description:**

This static website hosted on an S3 bucket, is regionally restricted, ensuring access only from specified locations *(e.g., India, etc.) .* Once logged in, the website provides custom file permissions based on user roles. User1, upon authentication, gains access to download various *.pdf files, while User2 can exclusively download *.txt files. This setup ensures a secure and tailored experience, granting access to specific file types based on individual user permissions, maintaining data integrity and user-specific file access.

## AWS Components Used:

- AWS Cognito
    - An **Amazon Cognito** user pool is a user directory for web and mobile app authentication and authorization. From the perspective of your app, an Amazon Cognito user pool is an OpenID Connect (OIDC) identity provider (IdP). A user pool adds layers of additional features for security, identity federation, app integration, and customization of the user experience.
    - You can, for example, verify that your users’ sessions are from trusted sources. You can combine the Amazon Cognito directory with an external identity provider. With your preferred AWS SDK, you can choose the API authorization model that works best for your app. And you can add AWS Lambda functions that modify or overhaul the default behavior of Amazon Cognito.
- S3 (Simple Storage Service)
    
    **Amazon Simple Storage Service (Amazon S3)** is an object storage service that offers industry-leading scalability, data availability, security, and performance. Customers of all sizes and industries can use Amazon S3 to store and protect any amount of data for a range of use cases, such as data lakes, websites, mobile applications, backup and restore, archive, enterprise applications, IoT devices, and big data analytics. Amazon S3 provides management features so that you can optimize, organize, and configure access to your data to meet your specific business, organizational, and compliance requirements.
    
- Lambda Function
    - **AWS Lambda** is a compute service that lets you run code without provisioning or managing servers.
    - Lambda runs your code on a high-availability compute infrastructure and performs all of the administration of the compute resources, including server and operating system maintenance, capacity provisioning and automatic scaling, and logging. With Lambda, all you need to do is supply your code in one of the language runtimes that Lambda supports.
- API Gateway
    - **Amazon API Gateway** is an AWS service for creating, publishing, maintaining, monitoring, and securing REST, HTTP, and WebSocket APIs at any scale. API developers can create APIs that access AWS or other web services, as well as data stored in the [AWS Cloud](https://aws.amazon.com/what-is-cloud-computing/). As an API Gateway API developer, you can create APIs for use in your own client applications.
- CloudFront
    
    **Amazon CloudFront** is a web service that speeds up distribution of your static and dynamic web content, such as .html, .css, .js, and image files, to your users. CloudFront delivers your content through a worldwide network of data centers called edge locations. When a user requests content that you're serving with CloudFront, the request is routed to the edge location that provides the lowest latency (time delay), so that content is delivered with the best possible performance.
    
- IAM (Identity Access Management)
    - **AWS Identity and Access Management (IAM)** is a web service that helps you securely control access to AWS resources. With IAM, you can centrally manage permissions that control which AWS resources users can access. You use IAM to control who is authenticated (signed in) and authorized (has permissions) to use resources.
    - When you create an AWS account, you begin with one sign-in identity that has complete access to all AWS services and resources in the account. This identity is called the AWS account *root user* and is accessed by signing in with the email address and password that you used to create the account. We strongly recommend that you don't use the root user for your everyday tasks. Safeguard your root user credentials and use them to perform the tasks that only the root user can perform.
    

## Key Feature Implemented:

- **Regional Lockdown:** Limits access to the S3-hosted website to specific regions (e.g., India), bolstering security and compliance.
- **User Authentication:** Enables secure access through a login system, allowing only authorized users to log in.
- **Role-Based Access Control(RABC):** Assigns distinct file access permissions to different user roles, allowing User1 to download *.pdf files and User2 to access *.txt files exclusively.
- **Data Integrity Management:** Ensures data integrity by restricting users to only authorized file types, preventing unauthorized access or tampering.
- **Customized User Experience:** Tailors the experience for users by granting access to specific file types based on their roles, maintaining data security and user-specific file access.

# Getting Started:

*In order to pursue this project these are the following up holding-hands you need to perform.*

## Prerequisite:

- AWS account to work around.
- Basic knowledge about AWS services.

## Steps to be followed:

### **Creating and configuring AWS Cognito:**

- Create a Cognito Userpool and follow-up the instruction below:
    - Set `**Cognito user pool sign-in options**` as email.
    - Set the password level as for your choice.
    - In the **`Required attributes`**  section, add attribute like name, phone number, preferred_username, Gender**.**
    - Set **`Configure message delivery`** as **`Send email with Cognito`**
    - Set the name as your choice say *demo-userpool* and check the `Use the Cognito Hosted UI` and set the preferred Cognito domain in your choice say *https://demo-userpool.auth.ap-south-1.amazoncognito.com*
    - Set the `App client type` as `Public Client` and keep the `Allowed callback URLs`  set as [www.youtube.com](http://www.youtube.com) (We need to change this after configuration of CloudFront distribution, done to be later).
     ****

### Creating and configuring S3:

- Create a S3 bucket set an unique name and set **`Object Ownership`** as **`ACLs enabled`** and set as **`Bucket owner preferred`** and disable the bucket key.
- Disable the `Bucket Key` option.
- Upload the `index.html` Page as below.

```html
<!doctype html>
<html lang="en" data-bs-theme="auto">

<head>
  

  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="description" content="">
  <meta name="author" content="Somya Ranjan Jena">
  <meta name="generator" content="Hugo 0.118.2">
  <title>Plivo-Project</title>
  <script src="https://sdk.amazonaws.com/js/aws-sdk-2.1078.0.min.js"></script>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet"
    integrity="sha384-T3c6CoIi6uLrA9TneNEoa7RxnatzjcDSCmG1MXxSR1GAsXEV/Dwwykc2MPK8M2HN" crossorigin="anonymous">

  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"
    integrity="sha384-C6RzsynM9kWDrMNeT87bh95OGNyZPhcTNXj1NW7RuBCsyN/o0jlpcV8Qyq46cDfL"
    crossorigin="anonymous"></script>
  <style>
    body {
      background-color: blanchedalmond;
    }
    .bd-placeholder-img {
      font-size: 1.125rem;
      text-anchor: middle;
      -webkit-user-select: none;
      -moz-user-select: none;
      user-select: none;
    }

    @media (min-width: 768px) {
      .bd-placeholder-img-lg {
        font-size: 3.5rem;
      }
    }

    .b-example-divider {
      width: 100%;
      height: 3rem;
      background-color: rgba(0, 0, 0, .1);
      border: solid rgba(0, 0, 0, .15);
      border-width: 1px 0;
      box-shadow: inset 0 .5em 1.5em rgba(0, 0, 0, .1), inset 0 .125em .5em rgba(0, 0, 0, .15);
    }

    .b-example-vr {
      flex-shrink: 0;
      width: 1.5rem;
      height: 100vh;
    }

    .bi {
      vertical-align: -.125em;
      fill: currentColor;
    }

    .nav-scroller {
      position: relative;
      z-index: 2;
      height: 2.75rem;
      overflow-y: hidden;
    }

    .nav-scroller .nav {
      display: flex;
      flex-wrap: nowrap;
      padding-bottom: 1rem;
      margin-top: -1px;
      overflow-x: auto;
      text-align: center;
      white-space: nowrap;
      -webkit-overflow-scrolling: touch;
    }

    .btn-bd-primary {
      --bd-violet-bg: #712cf9;
      --bd-violet-rgb: 112.520718, 44.062154, 249.437846;

      --bs-btn-font-weight: 600;
      --bs-btn-color: var(--bs-white);
      --bs-btn-bg: var(--bd-violet-bg);
      --bs-btn-border-color: var(--bd-violet-bg);
      --bs-btn-hover-color: var(--bs-white);
      --bs-btn-hover-bg: #6528e0;
      --bs-btn-hover-border-color: #6528e0;
      --bs-btn-focus-shadow-rgb: var(--bd-violet-rgb);
      --bs-btn-active-color: var(--bs-btn-hover-color);
      --bs-btn-active-bg: #5a23c8;
      --bs-btn-active-border-color: #5a23c8;
    }

    .bd-mode-toggle {
      z-index: 1500;
    }

    .main-div {
      height: 620px
    }

    .bd-mode-toggle .dropdown-menu .active .bi {
      display: block !important;
    }
  </style>

</head>

<body>

  <div class="container my-5">
    <div class="p-5 text-center bg-body-tertiary rounded-1 main-div">
      <!-- <svg class="bi mt-4 mb-3" style="color: var(--bs-indigo);" width="100" height="100"><use xlink:href="#bootstrap"/></svg> -->
      <img src="Upload image url here" class="mt-4 mb-3" style="height: 100px;" alt="Description of the image">

      <h1 class="text-body-emphasis">Sample Project</h1>
      <h3 class="text-body-emphasis">Regional Access-Controlled Static Website on S3 Bucket with Custom User File Permissions</h3>
      <p class="col-lg-8 mx-auto fs-10 text-muted">
        This static website, hosted on an S3 bucket, is regionally restricted, ensuring access only from specified locations (e.g., India, etc.). Once logged in, the website provides custom file permissions based on user roles. User1, upon authentication, gains access to download various *.txt files, while User2 can exclusively download *.pdf files. This setup ensures a secure and tailored experience, granting access to specific file types based on individual user permissions, maintaining data integrity and user-specific file access.
      </p>
      <div class="d-inline-flex gap-2 mb-5">
        <a class="d-inline-flex align-items-center btn btn-primary btn-lg px-4 rounded-pill" href="API-Endpoint-for-pdf">PDF File
        </a>
        <a class="btn btn-outline-secondary btn-lg px-4 rounded-pill" href="API-Endpoint-for-txt">
          TXT File
        </a>
      </div>
    </div>
  </div>

</body>

</html>
```

- Upload sample pdf and txt file in the same S3 Bucket.

### Creating and configuring Lambda Functions:

We need to create 3 functions:

1. AccessTokenFunction: To be used for getting the access token for the Cognito users.
2. PDFTriggerFunction: To be used when the user tries to access pdf files.
3. TXTTriggerFunction: To be used when the user tries to access txt files.

- Create the above three function such that set `Author from scratch`  and `Runtime` as `Python 3.11`

### *Code for AccessTokenFunction:*

```python
import os
import json
import boto3

s3 = boto3.client('s3')
def lambda_handler(event, context):

    # Send post authentication data to Cloudwatch logs
    user_attributes = event['request']['userAttributes']
    user_id = user_attributes.get('preferred_username')  # Example: Access user's unique identifier
    email = user_attributes.get('email')  # Example: Access user's email
    data_for_lambda2 = {
        'user_id': user_id,
        'email': email
    }
    bucket_name = 'bucket-name'
    object_key = 'data.json'  # The object key to store the data

    # Upload the data as JSON to the S3 bucket
    s3.put_object(
        Body=json.dumps(data_for_lambda2),
        Bucket=bucket_name,
        Key=object_key
    )
    # Return to Amazon Cognito
    return event
```

### *Code for PDFTriggerFunction:*

```python
import json
import boto3

s3 = boto3.client('s3')

def lambda_handler(event, context):
    bucket_name = 'srj-project-bucket'
    object_key = 'data.json'

    try:
        # Retrieve the data from the S3 bucket
        response = s3.get_object(Bucket=bucket_name, Key=object_key)
        data_from_s3 = json.loads(response['Body'].read())

        # Process the data as needed
        user_id = data_from_s3.get('user_id')

        if user_id == 'user1':
            pdf_object_key = 'pdf/somya1637.pdf'
            try:
                # Generate a pre-signed URL for the PDF file
                url = s3.generate_presigned_url(
                    ClientMethod='get_object',
                    Params={'Bucket': bucket_name, 'Key': pdf_object_key},
                    ExpiresIn=60  # URL expiration time in seconds (1 hour in this case)
                )

                return url
            except Exception as e:
                return {
                    'statusCode': 500,
                    'body': json.dumps({'message': 'Error generating URL', 'error': str(e)})
                }
        else:
            return {
                'statusCode': 403,
                'body': json.dumps({'message': 'Access denied'})
            }

    except Exception as e:
        return {
            'statusCode': 500,
            'body': json.dumps(f'Error: {str(e)}')
        }
```

### *Code for TXTTriggerFunction:*

```python
import json
import boto3

s3 = boto3.client('s3')

def lambda_handler(event, context):
    bucket_name = 'srj-project-bucket'
    object_key = 'data.json'

    try:
        # Retrieve the data from the S3 bucket
        response = s3.get_object(Bucket=bucket_name, Key=object_key)
        data_from_s3 = json.loads(response['Body'].read())

        # Process the data as needed
        user_id = data_from_s3.get('user_id')

        if user_id == 'user2':
            txt_object_key = 'txt/sample.txt'
            try:
                # Generate a pre-signed URL for the PDF file
                url = s3.generate_presigned_url(
                    ClientMethod='get_object',
                    Params={'Bucket': bucket_name, 'Key': txt_object_key},
                    ExpiresIn=3600  # URL expiration time in seconds (1 hour in this case)
                )

                return  url
                
            except Exception as e:
                return {
                    'statusCode': 500,
                    'body': json.dumps({'message': 'Error generating URL', 'error': str(e)})
                }
        else:
            return {
                'statusCode': 403,
                'body': json.dumps({'message': 'Access denied'})
            }

    except Exception as e:
        return {
            'statusCode': 500,
            'body': json.dumps(f'Error: {str(e)}')
        }
```

After configuring each function, deploy each function.

### Creating and configuring API Gateway:

 

- Create a new `REST API`  and set the unique name and also set the `API endpoint type` as `Regional`.
- Create two resource path as `\pdf` and `\txt`.
- Create two respective `GET` method in both the resource paths and the set the integration type as `Lambda function`.
- Set `PDFTriggerFunction` for `/pdf` and `TXTTriggerFunction` for `/txt`
- Deploy both the API endpoints, let txt endpoints be https://txt-endpoint and pdf endpoints be https://pdf-endpoint .

### Configuring Index.html for API Integrations:

- Edit the code in the html file and set the below:
- 

```html
<div class="d-inline-flex gap-2 mb-5">
        <a class="d-inline-flex align-items-center btn btn-primary btn-lg px-4 rounded-pill" href="https://pdf-endpoint">PDF File
        </a>
        <a class="btn btn-outline-secondary btn-lg px-4 rounded-pill" href="https://txt-endpoint">
          TXT File
        </a>
      </div>
```

### Configuring Cognito for USER ACCESS TOKEN trigger:

- Go to `User pool properties` , In the `Lambda trigger` section , click `Add Lambda trigger`
    1. Set the `Trigger type` as `Sign up` and `Post confirmation trigger`
        1. Set the lambda function as `AccessTokenFunction`
    2. Set the `Trigger type` as `Authentication` and `Post Authentication trigger`
        1. Set the lambda function as `AccessTokenFunction`

### Configuring CloudFront:

- Create a CloudFront distribution and set the `Origin path`  as your S3 bucket.
- In **`Cache key and origin requests`** , set it as S3.


