# Mweb Cloud Developer Test

## Introduction

Mweb, South Africa's leading Fibre ISP, is dedicated to connecting people with optimal technology solutions tailored to their needs.

Our IT infrastructure is pivotal in achieving this goal. Central to our systems is Solid, Mweb's OSS/BSS platform. Developed in Java, this monolithic application manages various business facets, including CRM, connectivity provisioning, ticketing, and billing. Despite its comprehensiveness, Solid's complexity poses challenges in terms of new development, often resulting in prolonged implementation times.

Since 2018, to complement and enhance Solid, we've adopted a microservice architecture for new functionalities. This approach, focusing each service on a single domain, simplifies development and maintenance. We chose Python as our programming language for its simplicity and robust feature set. Additionally, our extensive use of AWS managed services like Lambda, SQS, S3, and DynamoDB empowers our developers to concentrate on business logic, shifting focus away from the intricacies of infrastructure maintenance and scalability.

## The Challenge

Develop a REST API application that enables users to upload, download, and query CSV files on AWS S3. The application should provide the following functionalities:

### Signup or Login

- Implement endpoints for users to signup or login using their email and password.
- Implement basic security measures for user authentication.
- Email verification is not required but considered a bonus feature.

### List Files

- Create an endpoint to list CSV files stored in S3, accessible only to the logged-in user, ensuring data privacy and security.

### Upload a File

- Develop an endpoint to allow users to upload CSV files to S3.
- Store files in a user-specific folder, named after their email address.
- Include logic to handle file names that don’t match S3 object key restrictions.
- Define limits for file sizes and provide solutions for managing large files.

### Download a File

- Provide an endpoint for users to download their files, with safeguards against accessing others' files.

### Run a Query on a File

- Implement an endpoint using AWS S3 Select for running SQL queries on user-uploaded CSV files, returning results in CSV format.
- The query capabilities should align with those supported by S3 Select.

### Additional Requirements

- **REST API Focus**: The application should be strictly developed as a REST API.
- **Postman Collection**: Provide a comprehensive Postman collection to demonstrate and test all API endpoints, including various request and response scenarios.
- **Robust Error Handling**: Implement robust error handling with clear and informative response messages for all user interactions.
- **Security Prioritization**: Design the API with a strong emphasis on security, including measures like input validation and secure authentication protocols.
- **Email Verification**: Implementing email verification is optional but will be considered a value-added feature.
- **Testing and Documentation**: Conduct thorough unit and integration testing. Provide detailed documentation of the API, adhering to standard formats like OpenAPI/Swagger for API documentation.
- **Cost, Performance, and Scalability Analysis**: Assess and document the cost implications, performance metrics, and scalability capabilities of the application.
