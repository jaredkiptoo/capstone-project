# AWS Cloud Architecting Capstone Project: Deploying a Secure, Scalable PHP Web Application on AWS

## Introduction

### Project Title

AWS Cloud Architecting Capstone Project: Deploying a Secure, Scalable PHP Web Application on AWS

### Project Overview

This project focuses on migrating a PHP-based web application and its MySQL database to AWS to enhance performance, scalability, and security. The application, provided by the fictitious **Example Social Research Organization**, delivers global development statistics to researchers worldwide. By leveraging AWS services such as Amazon EC2, Amazon RDS, Application Load Balancer, Auto Scaling, and AWS Secrets Manager, the project implements a highly available and scalable architecture that addresses the need for improved performance and secure hosting.

- **Problem Solved**: Migrated an existing PHP web application to AWS to improve performance, scalability, and security.
- **Importance**: Ensures the application can handle increased traffic, is highly available, and maintains secure data access.
- **Primary AWS Architecture**: Deployed the application on EC2 instances in an Auto Scaling Group behind an Application Load Balancer, with an RDS MySQL database in private subnets, using AWS Secrets Manager for credentials management.

## Architecture Diagram



## Features and Functionality

### Key Features

- **Secure Hosting**: Deployed EC2 instances and RDS database in private subnets to prevent direct public access.
- **High Availability**: Utilized an Application Load Balancer and Auto Scaling Group across multiple Availability Zones.
- **Scalability**: Implemented Auto Scaling to adjust the number of EC2 instances based on traffic demand.
- **Cost Optimization**: Selected `t2.micro` instance types to balance performance and cost.
- **Data Migration**: Imported existing data into Amazon RDS MySQL to maintain data continuity.

### AWS Services Used

- **Amazon EC2**: Hosts the PHP web application on virtual servers.
- **Amazon RDS for MySQL**: Provides a managed MySQL database in a secure, private subnet.
- **Application Load Balancer (ALB)**: Distributes incoming HTTP traffic across multiple EC2 instances.
- **Auto Scaling Group (ASG)**: Automatically adjusts the number of EC2 instances based on demand.
- **AWS Secrets Manager**: Securely stores and manages database credentials.
- **Amazon VPC**: Offers a logically isolated virtual network with public and private subnets across multiple Availability Zones.
- **Security Groups**: Act as virtual firewalls to control inbound and outbound traffic to resources.
- **IAM Roles**: Manage permissions for AWS resources and services.
- **AWS CloudWatch**: Monitors resources and applications, providing actionable insights.

## Deployment

### Prerequisites

- **AWS Account**: An active AWS account with permissions to create and manage resources.
- **IAM User**: IAM user with administrative privileges or specific permissions for resource creation.
- **SSH Client**: For secure shell access to EC2 instances.
- **MySQL Client**: To import data into the RDS MySQL database.

### Step-by-Step Deployment Instructions

1. **Set Up VPC and Subnets**

   - Create a VPC or use an existing one.
   - Create public subnets (for ALB) and private subnets (for EC2 instances and RDS) across at least two Availability Zones.
   - Configure route tables, internet gateways, and NAT gateways as necessary.

2. **Create an Amazon RDS MySQL Database**

   - **Provision RDS Instance**:
     - Go to Amazon RDS in the AWS Management Console.
     - Choose "Create database" and select MySQL.
     - Use the "Free Tier" template if applicable.
     - Specify the `countries` database as the initial database.
     - Place the instance in the private subnet group.
   - **Security Groups**:
     - Create a security group for the RDS instance allowing inbound MySQL traffic (port 3306) only from the EC2 instances' security group.
   - **Credentials Management**:
     - Store the database username and password in AWS Secrets Manager.
     - Create a new secret of type "Credentials for RDS Database".
     - Attach an IAM role to EC2 instances to allow access to the secret.

3. **Create an Application Load Balancer**

   - Navigate to EC2 > Load Balancers in the AWS Management Console.
   - Create an Application Load Balancer:
     - Choose "Internet-facing" and select public subnets in multiple Availability Zones.
     - Configure a listener on port 80 for HTTP traffic.
   - **Target Group**:
     - Create a target group with the protocol HTTP and port 80.
     - Set the target type to "Instance".
   - **Security Groups**:
     - Assign a security group to the ALB allowing inbound HTTP traffic (port 80) from the internet.

4. **Set Up the Application Server in an Auto Scaling Group**

   - **Create Launch Template**:
     - Navigate to EC2 > Launch Templates.
     - Create a new launch template or use the provided `Project-LT`.
     - Include user data scripts to install PHP, configure the application, and retrieve credentials from Secrets Manager.
     - Assign an IAM role that allows access to AWS Secrets Manager.
   - **Configure Auto Scaling Group**:
     - Navigate to EC2 > Auto Scaling Groups.
     - Create a new ASG using the launch template.
     - Select private subnets across multiple Availability Zones.
     - Set desired capacity (e.g., min: 2, max: 4).
     - Attach the ASG to the ALB by selecting the target group.
   - **Scaling Policies**:
     - Implement a target tracking policy based on average CPU utilization.
   - **Security Groups**:
     - Create a security group for EC2 instances allowing:
       - Inbound HTTP traffic from the ALB security group.
       - Inbound SSH access from administrator IP addresses.
       - Outbound traffic to the internet and RDS on port 3306.

5. **Import Data into the RDS MySQL Database**

   - **Access the SQL Dump File**:
     - Locate the SQL dump file (`Countrydatadump.sql`) included in the project guidelines.
   - **Import Data**:
     - SSH into an EC2 instance within the VPC.
     - Install the MySQL client if necessary.
     - Retrieve database credentials from AWS Secrets Manager.
     - Run the following command to import data:
       ```bash
       mysql -h <RDS_Endpoint> -u <username> -p<password> countries < /path/to/Countrydatadump.sql
       ```
   - **Verify Data Import**:
     - Log in to the RDS instance using the MySQL client and run sample queries.

6. **Test the Application and Review Imported Data**

   - **Access the Application**:
     - Obtain the DNS name of the Application Load Balancer.
     - Open a web browser and navigate to the ALB DNS name.
   - **Test Functionality**:
     - Navigate through the application to ensure all pages load correctly.
     - Use search features to retrieve data from the database.
   - **Verify High Availability**:
     - Terminate an EC2 instance to simulate a failure and observe Auto Scaling behavior.
     - Ensure the application remains accessible during instance replacement.
   - **Security Verification**:
     - Attempt to access EC2 instances directly from the internet to confirm they are not publicly accessible.
     - Review security group rules and network ACLs.

## Security

- **Network Security**:
  - Deployed EC2 instances and RDS in private subnets.
  - Configured security groups to restrict inbound and outbound traffic.
  - Used NAT Gateways for EC2 instances to access the internet for updates.
- **IAM Roles and Policies**:
  - Attached IAM roles to EC2 instances for accessing Secrets Manager.
  - Implemented least privilege access for all IAM roles.
- **AWS Secrets Manager**:
  - Stored database credentials securely.
  - Retrieved secrets programmatically within the application.
- **Data Encryption**:
  - Enabled encryption at rest for RDS using AWS KMS.
  - Enforced SSL connections to RDS.
- **Application Security**:
  - Regularly updated software packages and dependencies.
  - Configured proper error handling to avoid information leakage.

## Testing and Validation

### Testing Strategy

- **Functional Testing**:
  - Verified web application functionality through user interface testing.
  - Tested database connectivity and data retrieval.
- **Load Testing**:
  - Used tools like Apache JMeter to simulate high traffic and observe scaling.
- **Failover Testing**:
  - Terminated EC2 instances to test Auto Scaling and ALB response.
- **Security Testing**:
  - Performed vulnerability scans using tools like OWASP ZAP.
  - Tested IAM policies and role assignments.
- **Monitoring and Logging**:
  - Configured CloudWatch alarms for critical metrics.
  - Reviewed logs for EC2, RDS, and ALB to identify issues.

### Testing Tools Used

- **AWS CloudWatch**: For monitoring and logging.
- **Apache JMeter**: For load testing.
- **MySQL Client**: For database verification.
- **SSH Clients**: For secure access to EC2 instances.

## Challenges and Learnings

- **Challenge**: Configuring security groups to allow necessary traffic while maintaining strict security.
  - **Learning**: Gained a deeper understanding of AWS networking and the importance of least privilege.
- **Challenge**: Implementing Auto Scaling with proper health checks.
  - **Learning**: Learned how to configure health checks to ensure instances are correctly identified as healthy or unhealthy.
- **Challenge**: Securely managing database credentials.
  - **Learning**: Gained experience using AWS Secrets Manager and IAM roles to enhance security.

## Future Improvements

- **Use AWS CloudFormation**: Automate infrastructure deployment with Infrastructure as Code.
- **Implement CI/CD Pipeline**: Use AWS CodePipeline and CodeDeploy for continuous integration and deployment.
- **Enhance Security with AWS WAF**: Protect the application from common web exploits and attacks.
- **Optimize Costs**: Explore the use of Reserved Instances or Spot Instances for cost savings.

## Contributors

- **JARED KIPTOO** - Architecture Design, Deployment, Security Configuration, Testing, and Documentation.

## License

This project is licensed under the MIT License.
