# Capstone Project-WordPress Site on AWS

## Evaluation Report for the Capstone Project: WordPress Site on AWS

Project Overview Diagram:

![alt text](image/Wordpress-project-Diagram.png)


**1. VPC SETUP**

#### Objective: Isolating and securing the WordPress infrastructure within a VPC.

Steps:

- Defining an IP Address Range for the VPC and create the VPC.

- Creating Public and Private Subnets across different Availability Zones for redundancy.

- Creating Internet Gateway (IGW):

- Providing a name for the Internet Gateway and attach it to the VPC.

- Creating a NAT Gateway, Choose the private subnet for the NAT Gateway and allocate an Elastic IP and create the NAT Gateway.

- Route Tables Configuration, Associate the public subnets with a route table that routes traffic to the IGW.

- Associate the private subnets with a route table that routes traffic to the NAT Gateway.

 Image showing the VPC resource map for the internet connectivities:

![alt text](image/VPC-Resource-MAP.png)


**2. BASTION HOST SETUP**

#### Objective: Setting up a secure access point to SSH into private EC2 instances.

Steps:

- Creating a Security Group for the Bastion Host allowing inbound SSH (port 22) from the IP address.

- Launching the Bastion Host in a public subnet using Amazon Linux 2 AMI.

- SSH into the Bastion Host from a terminal to securely access private instances.
 
 The images below shows how the bastion host was login successfully and how the private instance was accessed securely:

![alt text](image/Bastion-host-successfully-created.png)

![alt text](image/Access-EC2-Via-Bastion.png)


**3. WORDPRESS EC2 INSTANCE SETUP**

#### Objective: Deploying WordPress on a private EC2 instance.

- Creating a Security Group for WordPress allowing SSH, HTTP, and HTTPS access.

- Launching the EC2 Instance for WordPress in the private subnet with the necessary security group.

- Installing Apache, MySQL, WordPress and PHP on the EC2 instance.

Image showing how Apache was installed on my wordpress EC2 instance:

![alt text](image/installing-appache-httpd.png)

**4. AWS EFS SETUP**

#### Objective: Utilizing Amazon EFS for scalable and shared storage for WordPress files.

Steps: 

- Creating security group for EFS, ensuring that port 2049 (NFS) is opened, and allowing the worpress security group.

- Creating an EFS File System and ensure to attach the EFS security group and also attaching it to VPC (In private subnet).

- Installing NFS client on my EC2 instance, Creating Mount Targets in each private subnet for access.

- Mounting EFS to the WordPress EC2 instances for shared file storage.

Image below shows how the EFS was mounted to the Wordpress EC2 instance:

![alt text](image/Mounting-EFS..png)


**5. AWS RDS SETUP**

#### Objective: Deploying a managed MySQL database using Amazon RDS for WordPress data storage.

Steps:

- Creating a Security Group for the RDS instance allowing inbound traffic on MySQL port 3306 from the WordPress EC2 instances.

- Launching MySQL RDS Instance with appropriate configuration, such as storage and instance class.

- After creation the following are noted: RDS endpoint, default username and default password.

- Login with the default admin user above with the endpoint is the hostname. creating a database named (wordpress).

- Granting access to the database, and flushing privileges.

See below image:

![alt text](image/RDS-ACCESS-CREATE-DB-GRANT-FLUSH.png)

**6. CONNNECTING TO WORDPRESS TO RDS**

Steps:

- Renaming the default wp-config-sample.php to wp-config.php.

- Editing the wp-config.php file with the database details from the RDS instance (DB name, username, password, and host).

- Saving the file and restarting the web service.

**7. SETTING UP A LOAD BALANCER (LB)**

Since the wordpress is in the private subnet, I wont be able to access it using the public ip of the instance, Hence the need for Load Balancer.
Before setting up a load balancer, I will first create a Target Group which the Load Balancer will route traffic to, Our target group port wil be HTTP,
ALB facing internet, and placed in a public subnet.

After all this has been put in place, I was able to access the wordpress site through the LB DNS name, See images below.

![alt text](image/Wordpress.png)

![alt text](image/wordpress-DNS.png)


**8. Setting up Auto Scaling Group.**

Steps:

-  Creating an AMI (Machine Image) from my EC2 instance where all resources are available, this will be use when creating a template.

-  Create a Launch Template: The launch template specifies the configuration for my EC2 instances, including the Amazon Machine Image (AMI), instance   
   type, key, security groups, and some other settings.

-  Configuring the Auto Scaling Group: With the launch template available, Auto scaling group is created.

-  Selecting the VPC and subnets where the instances will operate. For high availability, it's advised to choose subnets across multiple Zones.

-  Setting Scaling Policies: Defining the desired, minimum, and maximum number of instances.

-  Configuring Load Balancing and Health Checks: Load balancer is associated to the ASG.

-  Setting up health checks to monitor instance health and ensure traffic is directed appropriately.
   See image below for health check configuration:

 ![alt text](image/Health-Check-Configuration.png)  

-  Verifying the Auto Scaling Group: This has to do with Monitoring the EC2 instances, Ensuring that the Auto Scaling Group launches the desired number of  
   instances, For this project i set my desired instances to 2, 1 as Minimum and 2 as Maximum. Also making sure that instances are distributed across the selected Zones.

   After ASG has been setup successfully, it launch at least one EC2 intance and when the workload increase on the instance it automatically launch another instance making it 2 instances so as to handle the traffic request, and at downtime period it shutdown an instance automatically.

The image below shows the health check status of my Target Group (EC2 Instance):

![alt text](image/Health-Check.png)











