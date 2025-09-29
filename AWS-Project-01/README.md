# AWS-Project-01: Infrastructure as Code (IaC) with Terraform to host a simple web server in AWS (Beginners)

![](https://i0.wp.com/skundunotes.com/wp-content/uploads/2021/11/53-image-0.png?fit=1200%2C673&ssl=1)

In this project, we will deploy a single EC2 instance on AWS using Terraform to host a simple web server. This involved setting up and configuring the necessary infrastructure via code, ensuring it’s accessible over the internet.

Let’s go! 🤘

**Technologies Used**

* **AWS**: As the cloud provider to host the infrastructure.
    
* **Terraform**: For infrastructure as code, to provision and manage the AWS resources.
    
* **Apache HTTP Server**: Chosen as the web server software to serve web content.
    

### **Prerequisites**

* An AWS account. -&gt; [You can create it here](https://aws.amazon.com/free/?gclid=CjwKCAjwxNW2BhAkEiwA24Cm9N-sK-j6kzHCMUkjptod89h_kAzhzIQHYkHesUDrpjPTtt8i3a4RFRoC52cQAvD_BwE&trk=9ab5159b-247d-4917-a0ec-ec01d1af6bf9&sc_channel=ps&ef_id=CjwKCAjwxNW2BhAkEiwA24Cm9N-sK-j6kzHCMUkjptod89h_kAzhzIQHYkHesUDrpjPTtt8i3a4RFRoC52cQAvD_BwE%3AG%3As&s_kwcid=AL%214422%213%21645133561110%21e%21%21g%21%21create+aws+account%2119579657595%21152087369744&all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free+Tier+Types=*all&awsf.Free+Tier+Categories=*all)
    
* Basic knowledge of AWS services (EC2, RDS, VPC, etc.).
    
* An IAM user with sufficient permissions to create and manage AWS resources.
    

### **Step 1: Setup Terraform Project**

What is Terraform?

> *HashiCorp Terraform is an infrastructure as code tool that lets you define both cloud and on-prem resources in human-readable configuration files that you can version, reuse, and share. You can then use a consistent workflow to provision and manage all of your infrastructure throughout its lifecycle. Terraform can manage low-level components like compute, storage, and networking resources, as well as high-level components like DNS entries and SaaS features.*

Terraform creates and manages resources on cloud platforms and other services through their application programming interfaces (APIs). Providers enable Terraform to work with virtually any platform or service with an accessible API.

[Source](https://developer.hashicorp.com/terraform/intro)

First, make a new directory for your Terraform project and navigate into it.

```bash
mkdir terraform-multi-tier-app
cd terraform-multi-tier-app
```

Initialise the directory with Terraform to prepare your working directory for other commands.

```bash
terraform init
```

![](https://miro.medium.com/v2/resize:fit:660/0*ofzWOWaIDiqJGis-)

### **Step 2: Define AWS Provider**

Create a file named provider.tf and specify your AWS provider and region.

```go
provider "aws" {
  region = "eu-west-1"  # Choose the region appropriate for you
}
```

For me, that’s eu-west-1

![](https://miro.medium.com/v2/resize:fit:396/0*5co8vSi4dHu9lyT7)

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*gbIbVCrUxb-WfZch)

### **Step 3: Create an EC2 Instance**

Create a file named main.tf.

```go
resource "aws_instance" "example" {
  ami        = "ami-04e49d62cf88738f1"  # Replace this with the latest Amazon Linux AMI in your region
  instance_type = "t2.micro"

  tags = {
 Name = "SimpleWebServer"
  }
}
```

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*3RNoJ4Ca3A5L-7HS)

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*nbXUu9gyh33PFVAb)

Automate the installation of a web server and serve a simple HTML page. Update the aws\_instance resource to include user data.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*JCEeCQwHpuSaCkYk)

### **Step 4: Apply Configuration**

Run these commands to launch your EC2 instance.

* **Plan**: Preview the changes Terraform will make.
    

```bash
terraform plan
```

So I received the following error:

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*o-XwbJvuNOXs6oo9)

To fix this, I ran “terraform init”

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*DUc-oxhb95BtiJSF)

Then I ran Terraform plan again and received the following error:

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*cpZeDmbZ3uRwKUDH)

This error happens because I haven’t connected Terraform to my AWS account yet. To do so, I decided to use CLI.

I used the official instruction from AWS -&gt; 💻[CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

Once I installed CLI, I used the following command:

```bash
aws configure
```

Then we have to generate an access key.  
Here are instructions on how to do it:

**Step 1:** Create a new access key, which includes a new secret access key.

* To create a new secret access key for your root account, use the [security credentials page](https://console.aws.amazon.com/iam/home?#security_credential). Expand the **Access Keys** section, and then click **Create New Root Key**.
    
* Open the IAM console to create a new secret access key for an IAM user. Click **Users** in the **Details** pane, click the appropriate IAM user, and then click **Create Access Key** on the **Security Credentials** tab.
    

**Step 2:** Download the newly created credentials, when prompted to do so in the key creation wizard.

[💻 Source](https://aws.amazon.com/blogs/security/wheres-my-secret-access-key/)

Here we provide our credentials to access our AWS account.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*fs3Njay-bLx5nHI5)

It works! 🚀  
It shows what ami will be used and what instance type will be created.  
Now I run the following command:

```plaintext
terraform plan
```

**Apply:** Create the resources on AWS. Now I run the following command:

```bash
terraform apply
```

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*pQ81T5gzUAE9u1Iw)

### **Step 5: Check Your Web Server**

At first, I was not able to access the public IP of this instance. That was because I had to add an HTTP inbound rule.

**Solution:**

* Go to the AWS Management Console.
    
* Navigate to the EC2 Dashboard and find your instance.
    
* Check the security group rules associated with the instance.
    
* Ensure there is an inbound rule that allows traffic on port 80 from your IP address or from all sources (0.0.0.0/0).
    

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*pH-LziT8FAheeOktsBlOPQ.png)

Once that’s done, the ec2 instance was accessible.  
**As you can see it works! 🚀🚀🚀**

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*GWZQ9o0y5GQ2FP7S)

### **Documentation and Best Practices**

Throughout the process, we documented each step, including troubleshooting methods and configuration choices. This not only aids in maintenance but also helps in scaling or adjusting the project later.

This project serves as a foundational experience in using Terraform to manage AWS resources, as well as in setting up basic web server hosting on the cloud. It can be extended or modified for more complex deployments in the future.

## 🛠️ **Author & Community**

This project is crafted by [**Harshhaa**](https://github.com/NotHarshhaa) 💡.  
I’d love to hear your feedback! Feel free to share your thoughts.

---

### 📧 **Connect with me:**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-%230077B5.svg?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/harshhaa-vardhan-reddy) [![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/NotHarshhaa) [![Telegram](https://img.shields.io/badge/Telegram-26A5E4?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/prodevopsguy) [![Dev.to](https://img.shields.io/badge/Dev.to-0A0A0A?style=for-the-badge&logo=dev.to&logoColor=white)](https://dev.to/notharshhaa) [![Hashnode](https://img.shields.io/badge/Hashnode-2962FF?style=for-the-badge&logo=hashnode&logoColor=white)](https://hashnode.com/@prodevopsguy)

---

### 📢 **Stay Connected**

![Follow Me](https://imgur.com/2j7GSPs.png)
