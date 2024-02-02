# Technical Interview Questions

The interview will focus on the following areas

## REST API

### What is a REST API?

A REST API is a set of rules and conventions for building and interacting with web services. It enables different software applications to communicate over the internet using a common set of protocols and standards. The key features and principles of REST APIs include:

1. **Client-Server Architecture**: The client and server are separate entities, allowing each to evolve independently.

2. **Statelessness**: Each request contains all the information needed to process it. The server does not store any client session information.

3. **Cacheability**: Responses must indicate their cacheability to enhance client-side performance.

4. **Uniform Interface**: The interface between clients and servers is consistent and standardized, typically using standard HTTP methods (GET, POST, PUT, DELETE, etc.).

5. **Layered System**: Clients cannot tell if they are communicating with the actual server or an intermediary. This supports load balancing and shared caches.

REST APIs are widely used in web services development due to their simplicity, scalability, and flexibility, enabling interactions between web-based applications and the creation of web services.

### Elaborate on the HTTP methods and there use cases

HTTP methods, also known as HTTP verbs, are integral to the HTTP protocol and REST API. They define the action to be performed on resources. Below are the commonly used HTTP methods and their typical use cases:

1. **GET**
   - **Use Case**: Retrieve data from a server.
   - **Characteristics**: Read-only, idempotent. Does not change the resource state.

2. **POST**
   - **Use Case**: Send data to create a new resource.
   - **Characteristics**: Used for creating new records. Not idempotent.

3. **PUT**
   - **Use Case**: Update an existing resource or create it if not present.
   - **Characteristics**: Idempotent. Repeated requests have the same effect.

4. **DELETE**
   - **Use Case**: Remove a resource.
   - **Characteristics**: Idempotent. Multiple requests have the same effect.

5. **PATCH**
   - **Use Case**: Apply partial updates to a resource.
   - **Characteristics**: Modifies only specified fields. Idempotency can vary.

6. **HEAD**
   - **Use Case**: Retrieve header information of a resource.
   - **Characteristics**: Used to check what a GET request will return.

7. **OPTIONS**
   - **Use Case**: Discover supported HTTP methods for a URL.
   - **Characteristics**: Used for checking server capabilities or CORS pre-flight requests.

Each method offers a distinct way to interact with resources in a RESTful system, enabling comprehensive CRUD (Create, Read, Update, Delete) operations.

## Questions about the code challenge

The challenge is to build a REST API that enables users to upload, download, and query CSV files on AWS S3. Discuss the AWS service(s) used to implement the following functionality, the reasons for your choice and the approximate cost of the service.

### Email Signup and Login

Implementing email signup and login using AWS involves several services, mainly Amazon Cognito for user authentication and management, and AWS Simple Email Service (SES) for handling email communications.


### How would you structure the S3 bucket to store files from multiple users?

To ensure each user in a multi-user environment can store files in an S3 bucket without accessing each other's files, follow these steps:

1. **Bucket Structure**
- Create a **top-level S3 bucket**.
- Within this bucket, create a **separate directory (prefix) for each user**. 
  - Example: `s3://your-bucket/user1/`, `s3://your-bucket/user2/`, etc.

2. **Access Control**
- Utilize **AWS Identity and Access Management (IAM)** for access control.
- Create **individual IAM roles or users** for each application user.
- Assign **policies** to these IAM roles/users that restrict access to their specific directory.
  - E.g., The IAM policy for `user1` should only allow actions (`s3:GetObject`, `s3:PutObject`) on `s3://your-bucket/user1/*`.

### How would you handle file names that don’t match S3 object key restrictions?

1. **Understand S3 Object Key Restrictions**
Object keys are essentially the file name in S3 and can include any UTF-8 character.
They should be URL encoded. For instance, spaces should be encoded as %20. The total key length must be between 1 and 1,024 bytes.

2. **Sanitize File Names**
Replace or remove characters that are not allowed or could cause issues. Common characters to watch out for include: &, $, @, =, :, +, ;, ,, ?, \, ^, {, }, [, ], " and '. Spaces can be either removed, replaced with underscores (_), or URL encoded.
Truncate or hash excessively long file names to keep within the 1,024-byte limit.

3. **URL Encoding**
URL encode the file names to ensure all characters are valid in a URL format, as S3 keys are used in object URLs.
Most programming languages offer libraries or functions to handle URL encoding.

4. **Maintain a Mapping**
If the original file names are important (for example, for display purposes), maintain a mapping between the original file name and the sanitized S3 object key.
This mapping can be stored in a database or as metadata with the S3 object.

5. **Test for Uniqueness**
Ensure that the sanitization process doesn’t create duplicate keys for different files. Incorporate unique identifiers (like UUIDs) if needed.

### Upload a File

While Lambda can be used for direct file uploads to S3, using pre-signed URLs for direct client-to-S3 uploads is more suitable for large files, offering scalability, efficiency, and cost-effectiveness.

#### Direct Upload from Lambda to S3
Lambda functions can be used for uploading files directly to an S3 bucket. However, this approach has several limitations, especially for large files:

Drawbacks:

1. **Lambda Execution Time Limit** 
AWS Lambda functions have a maximum execution time limit (15 minutes). Large file uploads might exceed this limit.

2. **Memory Constraints**
Lambda functions have memory limitations. Large files could consume significant memory.

3. **Increased Costs**
Lambda billing is based on execution time and memory. Longer execution times for large files lead to higher costs.

4. **Network Bandwidth**
File uploads consume Lambda's network bandwidth, potentially impacting other functions.

5. **Complexity in Handling Failures**
Managing failures and retries for large file uploads can be complex.

#### Using Pre-Signed URLs for Client-to-S3 Uploads

For large files, a more efficient approach is using pre-signed URLs. This involves generating a URL through Lambda, which the client uses to upload directly to S3.

Benefits:

1. **Reduced Load on Lambda**
Lambda is used only for generating the URL, minimizing execution time and resource usage.

2. **Bypassing Lambda Limitations**
Direct S3 uploads avoid Lambda's execution time and memory constraints.

3. **Improved Performance**
Direct client-to-S3 uploads are faster and reduce potential network bottlenecks.

4. **Scalability**
S3 can handle high volumes of upload requests without burdening Lambda.

5. **Cost-Effective**
Minimized Lambda usage leads to cost savings.

6. **Security and Access Control**
Pre-signed URLs are secure and can be set to expire, enhancing security.

7. **Simplified Error Handling**
Direct uploads simplify error handling by offloading it to the client-side.

### List Files

To list files in an S3 bucket, use the `list_objects_v2()` method from the AWS SDK for Python (Boto3). This method returns a list of objects in a bucket, including their metadata. To list files in a user-specific folder, specify the folder name as the `Prefix` parameter.

There are several important considerations to keep in mind:

1. **Prefix Usage**
The Prefix parameter is used to filter the list of objects to those with a specific prefix. Ensure the prefix is correctly specified to get the desired subset of objects. For instance, specifying prefix='folder1/' will list all objects in the folder1/ directory.

2. **Pagination**
S3's list_objects_v2 API call returns up to 1,000 objects per call by default. If your bucket contains more objects than this limit, the response will include a NextContinuationToken. You'll need to use this token in subsequent requests to retrieve the remaining objects.

3. **Performance Considerations**
Frequently listing large numbers of objects can impact performance. Consider how often you need to list objects and whether caching object listings is feasible for your use case.

4. **Cost Implications**
AWS charges for each list_objects_v2 request. Frequent calls, especially in buckets with a large number of objects, can increase costs.

5. **Consistency**
S3 offers eventual consistency for object listing. This means that very recent changes (such as newly added or deleted objects) might not immediately reflect in the list.

### Download a File

One should use a pre-signed URL to download a file from S3. This involves generating a URL through Lambda, which the client uses to download directly from S3.

### Run a Query on a File

To query a CSV file on S3, you can use Amazon S3 Select, a service that allows you to use SQL expressions to pull out only the data you need from an object stored in S3. S3 Select works with objects stored in CSV, JSON, or Apache Parquet format. It also works with objects that are compressed with GZIP or BZIP2 (for CSV and JSON objects only), and server-side encrypted objects.

```python
import boto3

s3 = boto3.client('s3')

resp = s3.select_object_content(
    Bucket='s3select-demo',
    Key='sample_data.csv',
    ExpressionType='SQL',
    Expression="SELECT * FROM s3object s WHERE s.\"Name\" = 'Jane'",
    InputSerialization = {'CSV': {"FileHeaderInfo": "Use"}},
    OutputSerialization = {'CSV': {}},
)

for event in resp['Payload']:
    if 'Records' in event:
        records = event['Records']['Payload'].decode('utf-8')
        print(records)
```

### Did you use Infrastructure as Code (IaC) to deploy your solution?

Infrastructure as Code (IaC) is a key practice in DevOps and cloud computing that involves managing and provisioning infrastructure through machine-readable definition files, rather than physical hardware configuration or interactive configuration tools. When deploying applications on AWS, using IaC offers several significant benefits:

1. **Consistency and Standardization**
IaC ensures a consistent environment setup. This avoids issues like "it works on my machine" since everyone uses the same configuration.  It standardizes the setup of infrastructure, reducing the chance of human error and inconsistencies.

2. **Version Control**
With IaC, infrastructure configurations can be version-controlled just like application code. This allows for tracking changes over time, understanding modifications, and rolling back if necessary.

3. **Automation and Efficiency**
IaC automates the provisioning and deployment process, reducing the time and effort required to manage infrastructure.
It enables rapid deployment of infrastructure and applications, increasing operational efficiency.

4. **Reusability**
IaC allows for the creation of reusable templates for infrastructure setup. This means you can replicate the same setup across different environments (development, staging, production) or projects.

5. **Scalability and Flexibility**
IaC makes it easier to scale infrastructure up or down based on demand. This is particularly beneficial in cloud environments like AWS where scalability is a key feature.  It also offers flexibility to change infrastructure components without significant overhead.

6. **Cost Management**
By automating and accurately provisioning what is needed, IaC helps in controlling and reducing unnecessary costs.  It also makes it easier to shut down unused resources, avoiding wastage of resources and money.

7. **Risk Reduction**
IaC improves security by enabling the configuration of security settings from the start.  It reduces the risk of downtime and increases reliability through consistent, repeatable processes.

8. **Documentation**
The code itself acts as documentation for your infrastructure. This makes it easier to understand the infrastructure setup and changes over time.

9. **Improved Disaster Recovery**
IaC makes it easier to recover from disasters. Since the entire infrastructure is defined as code, it can be quickly recreated in the event of a disaster.

10. **Compliance and Auditability**
IaC allows for easier compliance with regulatory standards, as the infrastructure can be set up with compliance requirements in mind.
The version-controlled nature of IaC provides an audit trail of infrastructure changes for compliance purposes

There are a number of IaC tools that can be used to deploy the solution, including AWS CloudFormation, AWS CDK, Terraform, and Serverless Framework.

### Did you tag your resources so that you can track your costs?

Tagging resources in AWS is a crucial practice for managing and organizing a wide array of services. When combined with Infrastructure as Code (IaC), it becomes an even more powerful tool, particularly for tracking and optimizing costs.

#### Understanding Tagging in AWS

Tags in AWS are key-value pairs associated with AWS resources. They allow you to categorize resources in different ways, such as by purpose, owner, or environment.

Tags can denote the owner (Owner: JohnDoe), purpose (Purpose: WebServer), environment (Env: Production), or any other relevant information.

It's important to establish a consistent tagging strategy across the organization for clarity and effectiveness.

#### Cost Tracking and Optimization

AWS uses tags to allocate costs on the AWS Cost Explorer. By tagging resources, you can break down AWS costs by project, department, application, etc.  This granular view of costs helps in identifying and eliminating unnecessary expenses.

Tags can be used to set budget alerts and forecasts for specific projects or departments, aiding in financial planning and management.



