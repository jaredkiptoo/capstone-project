AWS Academy Cloud Architecting Capstone Project

 Overview

This project involves migrating a PHP-based web application and its MySQL database to AWS to improve performance, scalability, and security. The application is provided by the fictitious Example Social Research Organization, which offers global development statistics to researchers.

 Objectives

- Deploy a highly available, scalable, and secure web application on AWS.
- Utilize AWS services such as Amazon EC2, Amazon RDS, Application Load Balancer, Auto Scaling, and AWS Secrets Manager.
- Ensure best practices in architecture design, including security, high availability, and cost-effectiveness.



 Solution Architecture

The solution leverages multiple AWS services to meet the requirements:

- Amazon EC2 instances running the PHP application in private subnets.
- Auto Scaling Group to manage EC2 instances across multiple Availability Zones.
- Application Load Balancer to distribute incoming traffic across EC2 instances.
- Amazon RDS for MySQL to host the database in a secure, private subnet.
- AWS Secrets Manager to securely store and manage database credentials.
- Amazon VPC with public and private subnets spread across multiple Availability Zones for high availability.



Solution Requirements

- Secure Hosting: The MySQL database should be securely hosted and accessible only by the web application.
- Public Access: The website must be publicly accessible without authentication.
- EC2 Instances: Run the website on `t2.micro` EC2 instances in private subnets, accessible via SSH for administrators.
- High Availability: Use a load balancer to distribute traffic and ensure high availability across multiple Availability Zones.
- Auto Scaling: Implement automatic scaling using a launch template.
- Use of AWS Services: Utilize AWS Secrets Manager for storing credentials and ensure the use of available AWS IAM roles.
- Data Migration: Import existing data into the Amazon RDS MySQL database from a SQL dump file.



 Implementation Steps

 1. Create an Amazon RDS MySQL Database

Provision RDS Instance:
  - Use Amazon RDS to create a MySQL database instance.
  - Select the `countries` database as the initial database.
  - Use the existing database subnet group provided in the lab.
  - Configure the instance to run in private subnets for security.
  Security Groups:
  - Create a security group that allows only the web application servers to access the database on the MySQL port (default 3306).
 Credentials Management:
  - Store the database username and password in AWS Secrets Manager for secure retrieval by the application.


 2. Create an Application Load Balancer

Provision Load Balancer:
  - Create an Application Load Balancer (ALB) in the public subnets across at least two Availability Zones.
  - Configure the ALB to listen on port 80 for HTTP traffic.
  Target Group:
  - Create a target group for the ALB to forward requests to the EC2 instances.
  Security Groups:
  - Assign a security group to the ALB that allows inbound HTTP traffic from the internet.


 3. Set Up the Application Server in an Auto Scaling Group

- Create Launch Template:
  - Use the provided Project-LT launch template.
  - Ensure it includes the necessary user data to install PHP and deploy the application code.
- Configure Auto Scaling Group:
  - Create an Auto Scaling Group (ASG) using the launch template.
  - Spread instances across multiple private subnets in different Availability Zones.
  - Set desired, minimum, and maximum capacity based on estimated traffic (e.g., min: 2, max: 4).
- Scaling Policies:
  - Implement a target tracking scaling policy (e.g., scale based on average CPU utilization).
- Security Groups:
  - Assign a security group that allows HTTP traffic from the ALB and SSH access for administrators.


 4. Import Data into the RDS MySQL Database

 Access the SQL Dump File:
  - Use the SQL dump file available on the EC2 instance or included in the launch template.
  Import Data:
  - Connect to the RDS instance using a MySQL client from an EC2 instance within the VPC.
  - Use the credentials from AWS Secrets Manager.
  - Run the SQL commands to import the data into the `countries` database.

 5. Test the Application and Review Imported Data

 Access the Application:
  - Use the DNS name of the Application Load Balancer to access the web application in a browser.
  Test Functionality:
  - Navigate through the application to ensure pages load correctly.
  - Use the query functionality to retrieve data and confirm that the database is connected and populated.
 Verify High Availability:
  - Simulate instance failures to test Auto Scaling and load balancing.
 Security Verification:
  - Ensure that the application servers are not publicly accessible directly from the internet.
  - Confirm that security groups and network ACLs are properly configured.



Design Decisions Summary

 Security:
  - Deployed EC2 instances in private subnets to prevent direct public access.
  - Used security groups to restrict database access to only the application servers.
  - Stored database credentials securely in AWS Secrets Manager.

  High Availability and Scalability:
  - Implemented an Auto Scaling Group across multiple Availability Zones to handle varying traffic loads.
  - Used an Application Load Balancer to distribute incoming traffic and improve fault tolerance.

  Cost Optimization:
  - Selected instance types (`t2.micro`) suitable for the workload to minimize costs.
  - Used Single-AZ deployment for the RDS instance due to budget considerations, acknowledging the trade-off with availability.

 Manageability:
  - Utilized a launch template for consistent EC2 instance configuration.
  - Automated the deployment process with user data scripts to install necessary software and deploy the application.

  Data Migration:
  - Imported existing data into Amazon RDS to maintain continuity of data and services.
