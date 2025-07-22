# AWS-lift-and-shift-vprofile using PAAS

------------------------------------------------------ 🧠 Project Summary ---------------------------------------------------
This project involves deploying a full-stack Java-based web application called VProfile on AWS using modern DevOps and cloud-native architecture. It follows a refactored approach by replacing traditional EC2-based services with managed AWS services and deploying the application using Elastic Beanstalk (PaaS) for ease and scalability.


-----------------------------------------------------⚙️ Architecture Overview -----------------------------------------------
The VProfile application is deployed on AWS using a fully managed and scalable architecture. Users access the application through a custom domain configured with Amazon Route 53, which acts as the DNS service. From there, requests are routed to Amazon CloudFront, a content delivery network (CDN) that improves performance and security by caching content at edge locations. CloudFront forwards these requests to an Elastic Load Balancer automatically provisioned by Elastic Beanstalk, which ensures even traffic distribution. The application itself is hosted in an Elastic Beanstalk environment using Tomcat and a Java .war file, providing a Platform-as-a-Service (PaaS) environment that simplifies application deployment, scaling, and monitoring. The Beanstalk application communicates with three backend services, all of which are fully managed by AWS but fall under PaaS rather than SaaS: Amazon RDS for MySQL-based database storage, Amazon MQ for message brokering via RabbitMQ, and ElastiCache for in-memory caching using Memcached. This layered architecture ensures high availability, scalability, and performance for the VProfile application.


   
---------------------------------------------- 🚀 Application Deployment (PaaS) --------------------------------------------
The web application is deployed using Elastic Beanstalk, a Platform as a Service (PaaS) provided by AWS.

It handles:
Provisioning EC2 instances
Creating Auto Scaling Group & Load Balancer
Monitoring using CloudWatch

The application code is packaged as a Java WAR artifact and deployed to Tomcat via Beanstalk.



------------------------------------- 🔗 Backend Service Integration (Managed Services) ----------------------------------
Amazon RDS (MySQL): Stores persistent application data.

Amazon MQ (RabbitMQ): Manages message brokering between services.

Amazon ElastiCache (Memcached): Provides caching for faster access to frequent data.

These replace previous EC2-hosted services and provide scalability, high availability, automated backups, and patching.



-------------------------------------------- 🌐 Domain & CDN Integration --------------------------------------------------
Amazon Route 53: Used for custom domain DNS management (e.g., vprofile.hkhinfo.xyz).

Amazon CloudFront:

Acts as a global CDN.

Caches content at edge locations for faster performance across geographies.

Integrated with HTTPS using a certificate from AWS Certificate Manager (ACM).

CloudFront Distribution is linked to the Load Balancer from Beanstalk.


----------------------------------------- 🧪 Artifact Creation and Delivery -----------------------------------------------
Java code was built using Maven on a local system.

The .war file was uploaded to Amazon S3.

Beanstalk fetches this artifact from S3 during deployment.



------------------------------------------------------- 🔐 IAM & Security -------------------------------------------------
IAM roles and policies were configured:

Beanstalk environment role with access to S3 and other services.

Security groups were created to restrict and allow access:

App SG → Backend SG (for DB, MQ, Cache access).

Load Balancer → App instance.

CloudFront → Load Balancer.



---------------------------------------------------- 📚 Key Concepts Learned ----------------------------------------------
Cloud-native architecture using PaaS and Managed Services.

Use of Elastic Beanstalk for simplified deployment and scaling.

Integration of CloudFront CDN for global performance.

Domain mapping and SSL with ACM.

CI/CD basics using Maven, S3, and deployment automation.

AWS service interconnectivity and security best practices.


