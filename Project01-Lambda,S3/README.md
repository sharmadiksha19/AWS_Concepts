# How to Read a CSV File from S3 Bucket in AWS Lambda

# Amazon S3 (Simple Storage Service):

- Amazon Simple Storage Service is a highly scalable, durable, and available object storage service.
- It is designed to store any amount of data, anytime, from anywhere on the web.
- S3 is a key component of the Amazon Web Services (AWS) cloud platform.

# Amazon Lambda:

- AWS Lambda is a serverless computing service that lets you run code without provisioning or managing servers.
- Lambda executes your code only when needed and scales automatically, from a few requests per day to thousands per second.
- You are billed only for the compute time you consume – there is no charge when your code is not running.

# More Details:

S3 and Lambda are two of the most popular AWS services. They can be used together to create robust, scalable, cost-effective applications.

S3 can be used to store data that is processed by Lambda functions.

Lambda's event-driven architecture seamlessly interfaces with S3 events, allowing developers to effortlessly trigger serverless functions in response to changes within S3 buckets. This allows you to build applications that automatically process data as it is added to S3.

# How to read a CSV file from an S3 bucket in AWS Lambda using the requests library or the boto3 library.

    # How to Create a Lambda Execution Role with S3 Read permissions:

    - For the Lambda service to read the files from the S3 bucket, you need to create a lambda execution role that has S3 read permissions.
    -To create a lambda execution role:
        --> 1. Sign in to the AWS Console and navigate to the Identity and Access Management (IAM) console
        --> 2. Click Roles and then click Create role:
        --> 3. Choose AWS service as a Trusted Entity and Lambda as the Use Case.
        --> 4. Add the AmazonS3ReadOnlyAccess policy for read-only S3 access.
        --> 5. Give the role a name and description and review the attached policies. Finally, click Create role to create the IAM role.
    - To Attach Role to the Lambda Function:
        --> 1. Go to your Lambda function in the Lambda console if it is already existing, or create a Lambda function if you need to create a new one
        --> 2. In the Execution role section, click Edit
        --> 3. Choose the IAM role you created
        --> 4. Save to attach the role to your Lambda function:

# How to Read a CSV File from S3 Bucket Using the Requests Library in AWS Lambda:

    #Request Library in python:
        - The Requests library is a popular Python module for making HTTP requests and interacting with web services.
        - With an easy-to-use and intuitive API, Requests allows developers to send GET, POST, PUT, DELETE, and other HTTP requests with minimal code. This makes it a valuable tool for tasks such as fetching web data, interacting with RESTful APIs, and more.
        - You can use the requests library to send a GET request and read a CSV file from S3 bucket.
        - Once the GET request is complete, you'll receive a response with appropriate response codes.If the get request is successful, it will return the status code 200. You can get the data from response.text.

        The following code demonstrates how to send the GET request and read the response:
        response = requests.get(url)
        if response.status_code == 200: # Parse the CSV data from the response content
        csv_data = response.text

        - use the CSV reader to read the CSV content from csv_data and iterate over the reader object to access each row of the CSV file

Code:
reader = csv.reader(csv_data.splitlines())
for row in reader:
print(row)

Complete Program:

      import requests
      import csv

      # URL of the CSV file
      url = "https://mrcloudgurudemo.s3.us-east-2.amazonaws.com/sample_csv.csv"
      try: # Send an HTTP GET request to the URL
      response = requests.get(url) # Check if the request was successful (HTTP status code 200)
      if response.status_code == 200: # Parse the CSV data from the response content
      csv_data = response.text # You can now process the CSV data as needed # For example, you can use the csv.reader to read the data
      reader = csv.reader(csv_data.splitlines()) # Iterate through the rows in the CSV
      for row in reader: # Process each row as needed
      print(row)
      else:
      print(f"Failed to fetch data. Status code: {response.status_code}")
      except requests.exceptions.RequestException as e:
      print(f"Error: {e}")

**Note**:if you’re using the requests library version 2.30.0 in AWS lambda, you might get a Cannot Import Name DEFAULT*CIPHERS from urllib3.util.ssl* error when any of the dependent libraries try to import the default_ciphers variable from urllib3.

You can solve this error by downgrading the requests library to 2.29.0.

# How to Read a CSV File Using the Boto3 Client get_Object() Method in AWS Lambda

- Boto3 is the AWS SDK for Python. It provides an object-oriented API as well as low-level access to AWS services.
- Boto3 makes it easy to create, configure, and manage AWS resources from your Python applications.
  For example, you can read the content of a file from an S3 bucket programmatically.[https://www.mrcloud.guru/reading-file-content-from-aws-s3-bucket-using-boto3/]
- Note that in order to interact with the AWS service using Boto3, you must configure the security credentials to avoid the unable to locate credentials error.[https://www.stackvidhya.com/fix-the-boto3-nocredentialserror-unable-to-locate-credentials-error/]
- You can configure the security credentials using the aws configure command.
  [https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3/client/get_object.html]
  -To read a CSV File Using the Boto3 Client get_Object() Method in AWS Lambda first create the Boto3 client:
  s3 = boto3.client('s3')
- Next, invoke the get_object() method from the Boto3 client and pass the bucket name and the object name:
  response = s3.get_object(Bucket=bucket_name, Key=file_key)
- Now, read the response body using response['Body'].read():
  csv_content = response['Body'].read().decode('utf-8')
- Use the Pandas library read_csv() method to parse the response text as CSV content and create a pandas dataframe out of it.
- You can also print the first five lines to see if the data is successfully read:
  df = pd.read_csv(io.StringIO(csv_content))
  print(df.head(5))

# The following code demonstrates the complete program to read a CSV file from S3 bucket using Boto3:

    import boto3
    import pandas as pd
    import io

    def lambda_handler(event, context): # Initialize the S3 client
    s3 = boto3.client('s3') # Specify the S3 bucket and object key of the CSV file
    bucket_name = 'dikshaS3'
    file_key = 'sample_csv.csv'
    try: # Read the CSV file from S3
    response = s3.get_object(Bucket=bucket_name, Key=file_key)
    csv_content = response['Body'].read().decode('utf-8') # Create a Pandas DataFrame
    df = pd.read_csv(io.StringIO(csv_content)) # Now you have your DataFrame (df) for further processing # Example: Print the first 5 rows
    print(df.head(5))
    return {
    'statusCode': 200,
    'body': 'File read successfully into DataFrame.'
    }
    except Exception as e:
    return {
    'statusCode': 500,
    'body': str(e)
    }
