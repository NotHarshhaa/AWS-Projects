# AWS-Project-02: WordPress Hosting Architecture using AWS VPC, EC2, RDS, and S3

We are going to introduce an AWS architecture for an application. This is a complete application that you will build step by step. With each module you complete, you will add a new service or resource to your architecture. But before we start, let's look at the prerequisites.

## **Prerequisites**

1. An AWS account with privileges to create IAM roles, AWS VPCs, EC2 instances, and RDS databases.

## **Problem**

In this lab, you will create the VPC in your AWS account. Before creating a VPC, you need to select a region, considering factors like cost, compliance, and latency. You will also need two subnets in your VPC: a public subnet to host your web application, where you will launch an EC2 instance, and a private subnet to deploy your RDS MySQL database. Then, you will set up the connection between RDS and EC2 and install the WordPress website on the instance. Finally, you will host a simple WordPress website on an EC2 instance and export the static assets to an S3 bucket.

## **Architecture**

![image](https://user-images.githubusercontent.com/63364609/225718740-959f0476-b0b0-4f21-bfb9-8bdc95fbb4fc.png)

• Create a VPC within your AWS account. But first, what exactly is a VPC? VPC stands for Virtual Private Cloud, which is a virtual network infrastructure provided by Amazon Web Services (AWS). It allows you to create a logically isolated section of the AWS cloud where you can launch various resources such as EC2 instances, RDS databases, and more. With a VPC, you have complete control over your network configuration, including the ability to define IP address ranges, create subnets, and manage routing tables. You can also establish security groups and network access control lists (ACLs) to regulate the traffic that flows to and from your instances, ensuring a secure environment.

A VPC enables you to extend your data centre into the cloud, providing a seamless and secure connection using an IPsec VPN or AWS Direct Connect. This setup allows for the integration of your VPC with other AWS services, such as Amazon S3 for storage, AWS Lambda for serverless computing, and many others, enhancing the functionality and scalability of your applications.

To get started, follow these steps:

**Step 1** – Log in to your AWS account. Once logged in, navigate to the AWS Management Console. In the search bar located at the top of the console, type "VPC" and select the VPC option from the dropdown menu. This will take you to the VPC dashboard, where you can begin setting up your Virtual Private Cloud.

![image](https://user-images.githubusercontent.com/63364609/225718899-c3b114af-a84f-411d-8403-bd3000c6b32c.png)

Before creating your own VPC, let's understand the default VPC. By default, when you create a new AWS account, a default VPC (Virtual Private Cloud) is automatically set up for you in each AWS region. The default VPC is a logically isolated virtual network within the AWS Cloud that you can use to launch AWS resources, such as EC2 instances, RDS databases, and more. It comes preconfigured with several default settings, including an Internet Gateway and a default subnet in each Availability Zone within the region. This means you can launch your resources in the default VPC without needing to configure networking settings.

![image](https://user-images.githubusercontent.com/63364609/225718961-68751fda-fd23-4314-b407-045ee7ec44a6.png)

**Step 2** - Once you are on the VPC dashboard, look for the option to create a new VPC. Click on the "Create VPC" button to start the process. You will be presented with different configuration options. For this step, choose the "VPC only" option, which allows you to create a Virtual Private Cloud without any additional components like subnets or gateways. Next, you need to enter the IPv4 CIDR block, which defines the range of IP addresses that will be available for your VPC. Ensure that you choose a CIDR block that suits your network requirements, keeping in mind the number of resources you plan to deploy within this VPC. Once you have entered the IPv4 CIDR block, review your settings and proceed to create the VPC.

![image](https://user-images.githubusercontent.com/63364609/225718998-ccac4f73-ccc9-4445-8a04-87a63b72f447.png)

• When you create a new VPC, a Main Route Table is automatically generated for you. This table is crucial for directing network traffic within your VPC. However, by default, it only includes a local route in its target group. This local route allows communication between resources within the VPC but does not enable any external connectivity. If you need your resources to communicate with the internet or other networks, you will need to manually add additional routes to the Main Route Table. These routes can include destinations such as an Internet Gateway for internet access or a Virtual Private Gateway for connecting to a VPN. It's important to carefully configure these routes to ensure that your network traffic is properly managed and secure.

![image](https://user-images.githubusercontent.com/63364609/225719021-56d6b620-0cbb-4467-a61c-397ba5b3bb22.png)

• Create a Public Subnet in Your Custom VPC

#### What is a Public Subnet?

A public subnet is a segment of a computer network that is designed to be accessible from the Internet. This means it has a public IP address that allows it to be reached by anyone on the Internet. Public subnets are crucial for deploying resources that need to be accessible to the public, such as web servers, email servers, and other services that require public interaction.

In cloud computing environments, such as Amazon Web Services (AWS), public subnets are often used in conjunction with private subnets to form a Virtual Private Cloud (VPC). This setup allows for a secure and organized network structure. The public subnet is typically used to host resources that need to be accessible from the Internet, providing a gateway for incoming traffic to reach these resources.

On the other hand, the private subnet is reserved for resources that should remain isolated from the public Internet. These resources might include databases, backend servers, and internal applications that require protection from external access. By using both public and private subnets within a VPC, organizations can effectively manage their network traffic, ensuring that public-facing services are accessible while sensitive data and applications remain secure and private.

This dual-subnet configuration allows for a flexible and scalable network architecture, enabling businesses to efficiently manage their resources while maintaining the necessary levels of security and accessibility.

![image](https://user-images.githubusercontent.com/63364609/225719058-d8f8ee10-5274-4b4b-a65d-e92a35358f7a.png)

In the image above, you can see the subnets that AWS automatically creates when a default VPC is set up. To create a new subnet, follow these steps:

**Step 1** – Click on "Create subnet." This will open a form where you can specify the details for your new subnet. First, select your custom VPC from the dropdown menu to ensure the subnet is part of the correct network. Next, give your subnet a meaningful name that reflects its purpose or location within your network architecture.

Then, choose an Availability Zone that suits your needs. This decision might depend on factors such as redundancy, latency, or specific resource requirements. After selecting the Availability Zone, assign an IPv4 CIDR block to the subnet. This block of IP addresses will define the range of addresses available for resources within this subnet. Make sure the CIDR block does not overlap with other subnets in your VPC to avoid IP address conflicts.

By carefully configuring these settings, you can ensure that your subnet is optimally set up to support your application's requirements.

![image](https://user-images.githubusercontent.com/63364609/225719080-72c8dec4-7b70-4f3a-9eff-d55041eb46da.png)

**Step 2** – Enable the "Auto-assign IP" feature. This setting is crucial because, as the name suggests, it designates the subnet as a Public subnet. By enabling this feature, any new resources launched within this subnet will automatically receive a public IP address. This is important for resources that need to be accessible from the internet, such as web servers or other public-facing applications. Ensuring that this option is enabled will help streamline the deployment process and ensure that your resources are properly configured for external access.

![image](https://user-images.githubusercontent.com/63364609/225719107-f47cea2b-da8f-4812-926e-da764fe899e2.png)

**Step 3** – Create an Internet Gateway for Your Custom VPC

To effectively connect your Virtual Private Cloud (VPC) to the internet, you need to create an Internet Gateway. But what exactly is an Internet Gateway? An Internet Gateway (IGW) is a crucial component in Amazon Web Services (AWS) that is designed to be horizontally scaled, redundant, and highly available. It serves as a virtual router that facilitates communication between resources within a VPC and the broader internet.

The Internet Gateway acts as a bridge, allowing traffic to flow in both directions. For outbound traffic, it provides a target for data packets that are destined for the public internet from instances within your VPC. This means that any resource, such as a web server, that needs to send data to users or services outside the VPC will route its traffic through the Internet Gateway.

Conversely, for inbound traffic, the Internet Gateway acts as a source for data originating from the internet and intended for instances within your VPC. This is essential for resources that need to be accessible from the outside world, such as public-facing applications or services.

Being an AWS-managed component, the Internet Gateway is attached directly to your VPC. This attachment is what enables the seamless flow of traffic between your VPC and the internet. By setting up an Internet Gateway, you ensure that your VPC can communicate with external networks, thereby extending its functionality and accessibility to users and services across the globe.

![image](https://user-images.githubusercontent.com/63364609/225719140-860f48f9-fbee-439b-a925-3e26af1b798e.png)

In the image above, you can see an Internet Gateway that has already been created. This gateway is automatically set up by AWS when the default VPC is created.

**To create a new Internet Gateway, follow these steps:**

1. Click on the "Create Internet Gateway" button in the AWS Management Console. This will initiate the process of setting up a new gateway for your VPC.

2. Provide a name for your new Internet Gateway. This name will help you identify it easily among other resources in your AWS environment.

3. After naming your Internet Gateway, proceed by clicking the "Create Internet Gateway" button to finalize the creation process.

Once created, you can attach this Internet Gateway to your VPC, enabling it to handle both inbound and outbound internet traffic for your resources. This setup is crucial for ensuring that your VPC can interact with the internet, allowing your applications and services to be accessible to users and systems outside of your private network.

![image](https://user-images.githubusercontent.com/63364609/225719197-9f66688f-9d0d-470f-b862-c0c1aae0e497.png)

• After successfully creating your new Internet Gateway, the next step is to attach it to your custom VPC. This process is essential as it allows your VPC to handle internet traffic, both incoming and outgoing. To do this, navigate to the "VPC" section in the AWS Management Console. Find your custom VPC from the list of available VPCs. Once you have located it, select the option to "Attach Internet Gateway." You will then be prompted to choose the Internet Gateway you just created. Select it from the list and confirm the attachment. This action will link your Internet Gateway to your VPC, enabling seamless communication between your VPC resources and the internet. This setup is vital for ensuring that your applications and services hosted within the VPC can be accessed by users and systems outside your private network, thereby expanding their reach and functionality.

![image](https://user-images.githubusercontent.com/63364609/225719226-a773ca2f-0b19-4bb4-ac7a-ef6fda7a8511.png)

![image](https://user-images.githubusercontent.com/63364609/225719254-3d80b98e-add3-4e87-929f-bbffb2c4758f.png)

![image](https://user-images.githubusercontent.com/63364609/225719275-d2a16a9f-11d2-4406-8d95-6bbfac462a61.png)

**Step 4** – Add Internet Gateway ID to the Main Route Table

**What is a Route Table?**

In Amazon Web Services (AWS), a route table is a critical component that determines how network traffic is directed within a Virtual Private Cloud (VPC). When you create a VPC, AWS automatically generates a main route table by default. This main route table is automatically associated with every subnet you create within the VPC unless you choose to associate a subnet with a custom route table instead.

The main route table contains a set of rules, known as routes, that define the paths network traffic should take to reach its destination. These routes are essential for managing how data moves between subnets within the VPC and for controlling access to and from the internet. By default, the main route table includes a route that directs all traffic to a local route, facilitating communication within the VPC itself.

However, to enable resources in a public subnet to access the internet, you need to add a specific route to the main route table. This involves adding a route that directs traffic to an Internet Gateway, which acts as a bridge between your VPC and the internet. Similarly, if you have resources in a private subnet that need internet access, you can add a route to a Network Address Translation (NAT) Gateway. This allows these resources to initiate outbound connections to the internet while remaining inaccessible from the outside world.

You have the flexibility to add, modify, or delete routes in the main route table to tailor the flow of traffic according to your network architecture and security requirements. By carefully configuring these routes, you can ensure that your VPC's resources are accessible as needed while maintaining control over network traffic and security.

![image](https://user-images.githubusercontent.com/63364609/225719377-9222e9e4-7a74-41b5-bec4-7430a8b1e8dd.png)

Select route table and go to edit routes in that add destination as 0.0.0.0/0 and past your internet gateway id in target and save the changes.

![image](https://user-images.githubusercontent.com/63364609/225719405-16e41b4b-9e99-4b58-aa93-7721105016c0.png)

• To create an EC2 instance in the public subnet of your custom VPC, follow these detailed steps:

1. **Launch Instance**: Begin by navigating to the EC2 dashboard in the AWS Management Console. Click on the "Launch Instance" button to start the process of creating a new EC2 instance.

2. **Name Your Instance**: Assign a meaningful name to your instance. This name will help you easily identify the instance among others in your AWS environment.

3. **Select Instance Type**: Choose an appropriate instance type based on your workload requirements. Instance types vary in terms of CPU, memory, storage, and networking capacity. For example, you might select a t2.micro for a small, low-cost instance or a larger type for more demanding applications.

4. **Create a Key Pair**: If you don't already have a key pair, create one. A key pair consists of a public key stored by AWS and a private key file that you store. This key pair is used to securely connect to your instance.

5. **Configure Network Settings**: In the network settings section, select your custom VPC from the dropdown menu. Ensure that you choose the public subnet within this VPC. This is crucial because a public subnet allows your instance to communicate with the internet, provided you have configured the necessary routes and Internet Gateway.

6. **Security Group Configuration**: Set up a security group to control the inbound and outbound traffic to your instance. For a public instance, you might allow SSH access from your IP address and HTTP/HTTPS traffic if you are hosting a web application.

7. **Review and Launch**: Carefully review all your settings to ensure everything is configured correctly. Once you are satisfied, click on the "Launch" button to create your instance.

8. **Connect to Your Instance**: After the instance is launched, you can connect to it using the private key file you downloaded earlier. Use an SSH client to establish a secure connection.

By following these steps, you will successfully create an EC2 instance in the public subnet of your custom VPC, ready for deployment and accessible from the internet.

![image](https://user-images.githubusercontent.com/63364609/225719440-3b1b794d-13f1-4ea1-bf61-988d444ef69c.png)

• Create a Private Subnet in Your Custom VPC

In Amazon Web Services (AWS), a private subnet is a part of a VPC that doesn't have a direct route to the Internet, meaning it isn't linked to an Internet Gateway. Instances in a private subnet can communicate with other instances in the same VPC or with other VPCs through a VPN or VPC peering connection. Typically, resources needing high security, like databases, backend servers, or internal applications, are placed in private subnets. Since they aren't directly accessible from the Internet, they are less exposed to public Internet attacks. They can only be accessed by authorized users or resources within the same VPC.

**Step 1** – Click on "Create a Subnet," then select your custom VPC. Name the subnet, choose an availability zone, and assign an IPv4 CIDR block to this subnet.

![image](https://user-images.githubusercontent.com/63364609/225719477-243b4274-4218-4eef-90f8-2bf1a94cf858.png)

Note:

* Auto-assign IP will be disabled because, as the name suggests, it is a private subnet.

* An Internet gateway is not required since it is a private subnet.

**Step 2** – Create a route table and keep only local targets in it.

![image](https://user-images.githubusercontent.com/63364609/225719540-52762f94-e6fc-4502-91e6-e50d82837dd0.png)

![image](https://user-images.githubusercontent.com/63364609/225719565-7e179e1e-b53e-4a8e-a23e-47c4faeac898.png)

![image](https://user-images.githubusercontent.com/63364609/225719588-6aa71451-b6cb-4396-8fec-db4332315dfc.png)

In below image you can see resource map, Public subnets are attached to Route table and Internet gateway, where Private subnets are attached to only Route table without Internet gateway access.

![image](https://user-images.githubusercontent.com/63364609/225719631-9500fcf4-058a-444b-aeac-74b860a5edbd.png)

•Create an RDS MySQL database in the private subnet of a custom VPC. RDS (Relational Database Service) is a web service from AWS (Amazon Web Services) that simplifies setting up, operating, and scaling a relational database in the cloud. RDS supports various database engines, including MySQL, PostgreSQL, Oracle, and SQL Server. It allows users to manage databases in the cloud without needing extensive hardware or software resources. RDS offers automated backups, software patching, and scalable storage, making it an efficient and cost-effective database management solution. When using RDS in AWS, users can choose from different instance types, which determine the computing and memory capacity of the database instance. RDS also supports read replicas, enabling users to create multiple copies of their database for read-intensive workloads.

**Step 1** - Before creating RDS, first create a subnet group consisting only of private subnets in RDS.

![image](https://user-images.githubusercontent.com/63364609/225719670-47c3f3ab-0b2d-4cfd-bf5f-038de60916d0.png)

**Step 2** – Create database by click on create database.

![image](https://user-images.githubusercontent.com/63364609/225719694-067c7af5-38cc-4868-bb0b-ea9f47ae28b9.png)

**Step 3** – Select Standard create, choose mysql and version, and I select free tier template

![image](https://user-images.githubusercontent.com/63364609/225719732-9b19a0bd-204f-4f34-9dc9-5e06e6c4192f.png)

![image](https://user-images.githubusercontent.com/63364609/225719762-000d59c8-35a3-427c-965c-308284378248.png)

**Step 4** – In setting type a name for your database, type master username and type of password.

![image](https://user-images.githubusercontent.com/63364609/225719784-a954e7a3-5ddc-408c-8be7-fbff5eb8d67a.png)

**Step 5** – In Instance configuration select Db instance class and select storage type and size.

![image](https://user-images.githubusercontent.com/63364609/225719804-0a8289a7-f5a8-45d9-8127-28fb2c790227.png)

**Step 6** – In connectivity choose your VPC and Public access NO.

![image](https://user-images.githubusercontent.com/63364609/225719836-d1ab763d-650a-40f1-97ea-40d694792c1a.png)

**Step 7** – Select Database authentication as password authentication, and click on click on database

![image](https://user-images.githubusercontent.com/63364609/225719861-1016d2a1-10ce-436a-bf56-35db82d06103.png)

![image](https://user-images.githubusercontent.com/63364609/225719881-1f155a4f-d9ae-40a4-860c-8e33ae75fd80.png)

**Step 8** - Now, let's move on and modify the security groups for the RDS and EC2 instances. We need an EC2 instance, so please create one along with any missing AWS resources we've set up so far.

* Select `public-instance-sg`.

* Click the "Edit inbound rules" button.

* Click "Add rule." For Type, select `MYSQL/Aurora`.

* For Source, select `custom` and find `assg-rds-mysql-sg`, then click "Save rules."

* Click "Add rule." For Type, select `HTTP`.

* For Source, select `My IP`, and click "Save rules."

![image](https://user-images.githubusercontent.com/63364609/225719905-5913be17-1dd1-4848-ac3c-0f9aa046c7a6.png)

* Select `db-sg`.

* Click the "Edit inbound rules" button.

* Delete the existing rule.

* Click "Add rule." For Type, select MYSQL/Aurora.

* For Source, select custom and find `public-instance-sg`, then click "Save rules."

![image](https://user-images.githubusercontent.com/63364609/225719925-32dd10a7-d2ba-4189-be81-5f8e602ead6b.png)

* Create an S3 bucket with a unique name.  
    Step 1 – Search for S3 in the search bar and create an S3 bucket with the name “assg-bucket-”.

![image](https://user-images.githubusercontent.com/63364609/225719960-4ae04212-b06b-4fdf-a5cb-bea411716d71.png)

**Step 2** – Block Public Access to this bucket and click on create bucket.

![image](https://user-images.githubusercontent.com/63364609/225719983-ae3faa8c-549c-449b-be68-229480789b63.png)

![image](https://user-images.githubusercontent.com/63364609/225719998-5b0bda78-3442-4130-bddf-54b9b9cd2c09.png)

• Set Up the WordPress Environment

**Step 1** - Select the public instance and click "Connect." On the "Connect to instance" page, click "SSH client" in the menu, and you will find the connection command in the example.

• Windows Users: Follow the steps in this guide to connect to the EC2 instance using PuTTY.

• MacOS Users: Open a terminal on your computer, navigate to the folder where your .pem key file is stored, and paste the connection command.

![image](https://user-images.githubusercontent.com/63364609/225720079-3a629cd2-992d-4651-9eaa-377f599cb60f.png)

Step 2 - After successfully connecting, run the following commands to install the LAMP stack (Linux, Apache, MariaDB, and PHP) on your instance:

* `sudo yum install httpd -y`

* `sudo service httpd start`

* `sudo service httpd status`

* `sudo yum install mariadb-server -y`

* `sudo service mariadb start`

* `sudo service mariadb status`

* `sudo amazon-linux-extras install php8.0 -y`

* `sudo service php-fpm start`

* `sudo service php-fpm status`

* `sudo service httpd restart`

* `sudo service mariadb restart`

* `sudo service php-fpm restart`

Here, I'm installing LAMP using a shell script, and we need to give execute permission to that LAMP file.

![image](https://user-images.githubusercontent.com/63364609/225720119-00c1884e-8bed-43d6-907a-dd0f7eb3e9e3.png)

![image](https://user-images.githubusercontent.com/63364609/225720138-eeaf4df1-925e-41c0-afcf-ea641936fd78.png)

**Step 3** - Set the MySQL environment variable on your computer. Replace it with the endpoint found in the RDS console for your database. Use the following command:  
`sudo mysql -h [endpoint] -u [username] -p`  
Then, create a database named `database1`.

![image](https://user-images.githubusercontent.com/63364609/225720178-5f7ddf63-2b82-45d2-a5ca-3c7ce6e411cd.png)

**Step 4** – Navigate to the html directory using the command `cd /var/www/html`. Then, download the WordPress module and unzip it with the following commands:

* `wget https://wordpress.org/latest.tar.gz`

* `tar -xzf latest.tar.gz`

![image](https://user-images.githubusercontent.com/63364609/225720203-5c27fc02-3b3d-47c1-8697-b7999a5f223d.png)

![image](https://user-images.githubusercontent.com/63364609/225720224-c78841a1-b97e-49a6-a78f-f05b73631a3f.png)

**Step 5** - Go into the WordPress folder and back up the default config file using the following commands:

```bash
cd wordpress
cp wp-config-sample.php wp-config.php
```

![image](https://user-images.githubusercontent.com/63364609/225720258-ee48bdd1-69d4-4fd4-8f44-c73466c72621.png)

**Step 6** - After that, use nano to edit the wp-config.php file with the command: `nano wp-config.php`

**Step 7** - Change the following values in the script:

* `DB_NAME`: 'wordpress'

* `DB_USER`: 'wordpress'

* `DB_PASSWORD`: 'wordpress-pass'

* `DB_HOST`: your RDS endpoint

Then, save the file.

![image](https://user-images.githubusercontent.com/63364609/225720299-69d5cb68-587d-4b57-b52e-cd6696b67c97.png)

**Step 8** - Select the EC2 instance, find the Public IPv4 DNS in the Details section below, and paste it into your browser. You will then see the WordPress setup page. On the setup page, enter your own values for Site Title, Username, Password, and Your Email, then click Install WordPress.

![image](https://user-images.githubusercontent.com/63364609/225720334-4120f4d9-5c07-4b2a-9269-2e8e14049511.png)

• After a few seconds, it will redirect to the Login page.  
• Enter your username and password, and click the Login button to see the admin page.  
• You can use the Admin dashboard to enhance your blog.  
• Now, you can view your blog in your browser.

![image](https://user-images.githubusercontent.com/63364609/225720368-646334a3-2ff1-4c95-93f5-cd24b97c7f04.png)

Congratulations on successfully hosting a WordPress website on an EC2 instance! By following the steps outlined, you have configured all the necessary AWS services to get your site up and running. This process involved downloading and setting up WordPress, configuring the database connection, and ensuring that your EC2 instance is properly linked with your RDS database. You also learned how to edit configuration files and navigate the WordPress setup page to customize your site with a unique title, username, password, and email address.

Once the setup was complete, you accessed the WordPress admin dashboard, where you can manage and enhance your blog with various themes and plugins. This dashboard is a powerful tool that allows you to customize the appearance and functionality of your site to suit your needs.

If you encounter any issues or have further questions about managing your WordPress site or AWS services, feel free to leave a message here. We are here to help you with any challenges you might face as you continue to develop and improve your website.

## 🛠️ **Author & Community**

This project is crafted by [**Harshhaa**](https://github.com/NotHarshhaa) 💡.  
I’d love to hear your feedback! Feel free to share your thoughts.

---

### 📧 **Connect with me:**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-%230077B5.svg?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/harshhaa-vardhan-reddy) [![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/NotHarshhaa) [![Telegram](https://img.shields.io/badge/Telegram-26A5E4?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/prodevopsguy) [![Dev.to](https://img.shields.io/badge/Dev.to-0A0A0A?style=for-the-badge&logo=dev.to&logoColor=white)](https://dev.to/notharshhaa) [![Hashnode](https://img.shields.io/badge/Hashnode-2962FF?style=for-the-badge&logo=hashnode&logoColor=white)](https://hashnode.com/@prodevopsguy)

---

### 📢 **Stay Connected**

![Follow Me](https://imgur.com/2j7GSPs.png)
