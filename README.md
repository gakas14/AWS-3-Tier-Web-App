# AWS-3-Tier-Web-App
This template installs a highly available, scalable Nginx webserver using a multi-az Amazon RDS database instance for storage.

I. Create a s3 bucket and an IAM role

  - S3 bucket

<img width="1219" alt="Screen Shot 2023-12-05 at 12 27 48 PM" src="https://github.com/gakas14/AWS-3-Tier-Web-App/assets/74584964/f14f96fe-13db-4c20-82e3-9f98a7ee0e12">



  - IAM role with AmazonSSMManagedInstanceCore and AmazonS3ReadOnlyAccess. These policies will allow our instances to download our code from S3 and use Systems Manager Session Manager to securely connect to our instances without SSH keys through the AWS console.

    <img width="1260" alt="Screen Shot 2023-12-05 at 12 28 02 PM" src="https://github.com/gakas14/AWS-3-Tier-Web-App/assets/74584964/17b73f89-e67f-4d29-a14d-29666464dfb4">




II. Networking

  1. VPC
     
     <img width="1280" alt="Screen Shot 2023-12-05 at 12 28 48 PM" src="https://github.com/gakas14/AWS-3-Tier-Web-App/assets/74584964/a63802a8-d308-4eae-8a8c-b6f2070154f7">

  


  2. Subnets:
       - 6 subnets:
       - Public-Web-Subnet-AZ-1, Private-App-Subnet-AZ-1, Private-DB-Subnet-AZ-1, Public-Web-Subnet-AZ-2, Private-App-Subnet-AZ-2, Private-DB-Subnet-AZ-2.



<img width="1267" alt="Screen Shot 2023-12-05 at 12 27 22 PM" src="https://github.com/gakas14/AWS-3-Tier-Web-App/assets/74584964/5b04a03b-3859-43ee-a1e3-0de5b020ce53">




  3. Route Tables


  
  <img width="1274" alt="Screen Shot 2023-12-05 at 12 31 28 PM" src="https://github.com/gakas14/AWS-3-Tier-Web-App/assets/74584964/20084e98-dea5-43c3-b1b7-3422e9577b9d">





  4. Internet Gateway:
       - Attach the internet gateway to the vpc


       <img width="1280" alt="Screen Shot 2023-12-05 at 12 29 42 PM" src="https://github.com/gakas14/AWS-3-Tier-Web-App/assets/74584964/01aa875b-e489-4347-980c-d65e4d8a576c">




     
  6. NAT gateway:
       - create two NAT Gateway in the two public az
     

     <img width="1278" alt="Screen Shot 2023-12-05 at 12 30 04 PM" src="https://github.com/gakas14/AWS-3-Tier-Web-App/assets/74584964/9455a2ee-00b5-4f42-8776-b43b526ee150">




  7. Security Groups: 5 security groups
       - The first security group you’ll create is for the public, internet-facing load balancer. After typing a name and description, add an inbound rule to allow HTTP-type traffic for your IP.
       - The second security group you’ll create is for the public instances in the web tier. After typing a name and description, add an inbound rule that allows HTTP-type traffic from your internet-facing load balancer security group you created in the previous step. This will allow traffic from your public-facing load balancer to hit your instances. Then, add an additional rule allowing HTTP-type traffic for your IP. This will allow you to access your instance when we test.
       - The third security group will be for our internal load balancer. Create this new security group and add an inbound rule that allows HTTP-type traffic from your public instance security group. This will allow traffic from your web tier instances to hit your internal load balancer.
       - The fourth security group we’ll configure is for our private instances. After typing a name and description, add an inbound rule allowing TCP-type traffic on port 4000 from the internal load balancer security group you created in the previous step. Our app tier application is running on this port, allowing our internal load balancer to forward traffic on this port to our private instances. You should also add another route for port 4000 that allows your IP for testing.
       - The fifth security group we’ll configure protects our private database instances. For this security group, add an inbound rule that will allow traffic from the private instance security group to the MYSQL/Aurora port (3306).




III. Deploy Database Layer


  1. Subnet Groups

<img width="1278" alt="Screen Shot 2023-12-05 at 12 51 57 PM" src="https://github.com/gakas14/AWS-3-Tier-Web-App/assets/74584964/f5daea76-2845-400f-83a9-df13617e34b5">




  2. Multi-AZ Database
      
<img width="1278" alt="Screen Shot 2023-12-05 at 12 52 16 PM" src="https://github.com/gakas14/AWS-3-Tier-Web-App/assets/74584964/1e43228c-0541-41d2-bc68-13ffc631d559">

