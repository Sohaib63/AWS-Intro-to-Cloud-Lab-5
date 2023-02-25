# Lab 5: Creating a Virtual Private Cloud

## Lab Overview

Traditional networking is difficult. It involves equipment, cabling, complex configurations, and specialist skills. Amazon Virtual Private Cloud (Amazon VPC) hides the complexity and simplifies the deployment of secure private networks.

This lab shows you how to:

- Deploy a VPC
- Create an internet gateway and attach it to the VPC
- Create a public subnet
- Create a private subnet
- Create an application server to test the VPC

At the end of this lab, your architecture will look like the following example:

![Architecture](image.jpg)

## Duration

This lab requires approximately 45 minutes to complete.

## Prerequisites

This lab requires:

- Access to a notebook computer with Wi-Fi running Microsoft Windows, macOS, or Linux (Ubuntu, SUSE, or Red Hat)
- For Microsoft Windows users, administrator access to the computer
- An Internet browser such as Chrome or Firefox.

Note: This lab is incompatible with Internet Explorer 11. Use a different browser to launch this lab.

## AWS Service Restrictions

In this lab environment, access to AWS services and service actions might be restricted to only the ones that you need to complete the lab instructions. You might encounter errors if you attempt to access other services or perform actions beyond the ones that this lab describes.

## Accessing the AWS Management Console

1. At the top of these instructions, select Start Lab to launch your lab.

2. A Start Lab panel opens displaying the lab status.

3. Wait until you see the message Lab status: ready, and then choose the X to close the Start Lab panel.

4. At the top of these instructions, choose AWS.

5. The AWS Management Console opens in a new browser tab. The system automatically signs you in.

Tip: If a new browser tab does not open, a banner or icon at the top of your browser typically indicates that your browser is preventing the site from opening pop-up windows. Select the banner or icon, and choose Allow pop-ups.

6. Arrange the AWS Management Console tab so that it displays along side these instructions. Ideally, you should be able to see both browser tabs at the same time to make it easier to follow the lab steps.

Do not change the Region unless specifically instructed to do so.

# Task 1: Creating a VPC

You begin by using Amazon VPC to create a new VPC.

A VPC is a virtual network that is dedicated to your Amazon Web Services (AWS) account. It is logically isolated from other virtual networks in the AWS Cloud. You can launch AWS resources, such as Amazon Elastic Compute Cloud (Amazon EC2) instances, into the VPC. You can configure the VPC by modifying its IP address range and can create subnets. You can also configure route tables, network gateways, and security settings.

## Creating the VPC

1. In the AWS Management Console, on the Services menu, choose VPC.
2. In the left navigation pane, choose Your VPCs.
3. Choose Create VPC.
4. Configure the following settings:
   - For Name tag, enter "Lab VPC".
   - For IPv4 CIDR block, enter "10.0.0.0/16".
5. Choose Create VPC.

Note: If these options do not appear, cancel your configuration. In the left navigation pane, make sure you that chose Your VPCs. Then, choose Create VPC again.

## Configuring DNS Hostnames

1. From the VPC Details page, choose the Tags tab.
2. Choose Actions and select Edit DNS hostnames.
3. Select Enable, and choose Save changes.

This option assigns a friendly Domain Name System (DNS) name to EC2 instances in the VPC, such as the following:

`ec2-52-42-133-255.us-west-2.compute.amazonaws.com`

Any EC2 instances that are launched into the VPC now automatically receive a DNS hostname. You can also add a more-meaningful DNS name (such as "app.example.com") later by using Amazon Route 53.

# Task 2: Creating subnets

A **subnet** is a subrange of IP addresses in the VPC. AWS resources can be launched into a specified subnet. Use a public subnet for resources that must be connected to the internet, and use a private subnet for resources that must remain isolated from the internet.

In this task, you create a public subnet and a private subnet:

## Subnets

### Create a public subnet

You use the public subnet for internet-facing resources.

1. In the left navigation pane, choose **Subnets**.
2. Choose **Create subnet** and configure the following settings:

   - For VPC ID, choose Lab VPC.
   - For Subnet name, enter Public Subnet.
   - For Availability zone, select the first Availability Zone in the list. Do not choose No Preference.
   - For IPv4 CIDR block, enter 10.0.0.0/24.
   
3. Choose **Create subnet**.
   
The VPC has a CIDR block of 10.0.0.0/16, which includes all 10.0.x.x IP addresses. The subnet you just created has a CIDR block of 10.0.0.0/24, which includes all 10.0.0.x IP addresses. They might look similar, but the subnet is smaller than the VPC because of the /24 in the CIDR range.

You now configure the subnet to automatically assign a public IP address for all instances that are launched in it.

4. Select the check box for Public Subnet.
5. Choose **Actions** and select **Edit subnet settings**. Then configure the following option:
   
   - Under Auto-assign IP settings, select Enable auto-assign public IPv4 address.
   
6. Choose **Save**.

Though this subnet is named Public Subnet, it is not yet public. A public subnet must have an internet gateway, which you attach in the next task.

### Create a private subnet

You use the private subnet for resources that must remain isolated from the internet.

Use what you learned in the previous steps to create another subnet with the following settings:

1. For VPC ID, choose Lab VPC.
2. For Subnet name, enter Private Subnet.
3. For Availability Zone, select the first Availability Zone in the list. Do not choose No Preference.
4. For IPv4 CIDR block, enter 10.0.2.0/23.
5. Choose **Create subnet**.

The CIDR block of 10.0.2.0/23 includes all IP addresses that start with 10.0.2.x and 10.0.3.x. This is twice as large as the public subnet because most resources should be kept private unless they specifically must be accessible from the internet.

Your VPC now has two subnets. However, the public subnet is totally isolated and cannot communicate with resources outside the VPC. Next, you configure the public subnet to connect to the internet via an internet gateway.

# Task 3: Creating an internet gateway

An internet gateway is a horizontally scaled, redundant, and highly available VPC component. It allows communication between the instances in a VPC and the internet. It imposes no availability risks or bandwidth constraints on network traffic.

An internet gateway serves two purposes:

1. To provide a target in route tables that connects to the internet
2. To perform network address translation (NAT) for instances that were assigned public IPv4 addresses

In this task, you will create an internet gateway so that internet traffic can access the public subnet.

## Steps

1. In the left navigation pane, choose **Internet Gateways**.
2. Choose **Create internet gateway** and configure the following settings:
   - For Name tag, enter `Lab IGW`
   - Choose **Create internet gateway**
3. You can now attach the internet gateway to your Lab VPC.
4. Choose **Actions** and then **Attach to VPC**, and configure the following settings:
   - For Available VPCs, select **Lab VPC**.
   - Choose **Attach internet gateway**
5. This action attaches the internet gateway to your Lab VPC. Although you created an internet gateway and attached it to your VPC, you must also configure the public subnet route table so that it uses the internet gateway.

# Task 4: Configuring Route Tables

A route table contains a set of rules, called routes, that are used to determine where network traffic is directed. Each subnet in a VPC must be associated with a route table because the table controls the routing for the subnet. A subnet can be associated with only one route table at a time, but you can associate multiple subnets with the same route table.

To use an internet gateway, a subnet's route table must contain a route that directs internet-bound traffic to the internet gateway. If a subnet is associated with a route table that has a route to an internet gateway, it is known as a public subnet.

In this task, you:

1. Create a public route table for internet-bound traffic
2. Add a route to the route table to direct internet-bound traffic to the internet gateway
3. Associate the public subnet with the new route table

## Steps:

1. In the left navigation pane, choose Route Tables.
2. Several route tables are displayed, but there is only one route table associated with Lab VPC. This route table routes traffic locally, so it is a private route table.
3. In the VPC column, find the route table that shows Lab VPC, and select the check box for this route table. (You can expand the column to see the names.)
4. In the Name column, choose <i class="fas fa-pencil-alt"></i> and then enter the name "Private Route Table" and choose Save
5. In the lower half of the page, choose the Routes tab.
6. There is only one route. It shows that all traffic that is destined for 10.0.0.0/16 (which is the range of the Lab VPC) will be routed locally. This route allows all subnets in a VPC to communicate with each other.
7. You now create a new public route table to send public traffic to the internet gateway.
8. Choose Create route table and configure the following settings:
   - For Name, enter "Public Route Table"
   - For VPC, choose Lab VPC.
   - Choose Create route table
9. In the Routes tab, choose Edit routes
10. You now add a route to direct internet-bound traffic (0.0.0.0/0) to the internet gateway.
11. Choose Add route and then configure the following settings:
   - For Destination, enter 0.0.0.0/0
   - For Target, select Internet Gateway, and then from the dropdown list select Lab IGW.
   - Choose Save changes
12. The last step associates this new route table with the public subnet.
13. Choose the Subnet associations tab.
14. In the Subnets without explicit associations section, choose Edit subnet associations
15. Select the row with Public Subnet.
16. Choose Save associations

The public subnet is now public because it has a route table entry that sends traffic to the internet via the internet gateway.

To summarize, you can create a public subnet by following these steps:

1. Create an internet gateway.
2. Create a route table.
3. Add a route to the route table that directs 0.0.0.0/0 traffic to the internet gateway.
4. Associate the route table with a subnet, which then becomes a public subnet.

# Task 5: Creating a security group for the application server

A security group acts as a virtual firewall for instances to control inbound and outbound traffic. Security groups operate at the level of the elastic network interface for the instance. Security groups do not operate at the subnet level. Thus, each instance can have its own firewall that controls traffic. If you do not specify a particular security group at launch time, the instance is automatically assigned to the default security group for the VPC.

In this task, you create a security group that allows users to access your application server via HTTP.

1. In the left navigation pane, choose Security Groups.
2. Choose **Create security group** and configure the following settings:
    * For Security group name, enter `App-SG`
    * For Description, enter `Allow HTTP traffic`
    * For VPC, choose `Lab VPC`.
3. Choose **Create security group**
4. Choose the **Inbound Rules** tab.
5. The settings for Inbound Rules determine what traffic is permitted to reach the instance. You configure it to permit HTTP (port 80) traffic that comes from anywhere on the internet (0.0.0.0/0).
6. Choose **Edit inbound rules**
7. Choose **Add rule** and then configure the following settings:
    * For Type, choose `HTTP`.
    * From the Source type dropdown list, choose `Anywhere IPv4`.
    * For Description, enter `Allow web access`
8. Choose **Save rules**

You use this `App-SG` in the next task.

# Task 6: Launching an application server in the public subnet
1. On the Services menu, choose EC2.
2. Choose Launch instance and then select Launch instance from the dropdown list. Configure the following options:
    - In the Name and tags pane, in the Name text box, enter App Server
    - In the Application and OS Images (Amazon Machine Image) section, keep default selection, Amazon Linux 2.
    - In the Instance type section, keep the default instance type, t2.micro.
    - In the Key pair (login) section, from the Key pair name - required dropdown list, choose Proceed without a key pair (not recommended).
    - In the Network settings section, choose Edit
        - From the VPC - required dropdown list, choose Lab VPC.
        - From the Subnet dropdown list, choose Public Subnet.
        - Ensure that Auto-assign public IP is Enable.
        - In the Firewall (security groups) section, choose Select existing security group
        - From the Common security groups dropdown list, choose App-SG.
    - In the Configure storage section, keep the default storage configuration.
    - Expand the Advanced details section.
    - For IAM instance profile, choose the role Inventory-App-Role.
    - Scroll down to User data section, copy and paste the below code in the block.

        ```
        #!/bin/bash
        # Install Apache Web Server and PHP
        yum install -y httpd mysql
        amazon-linux-extras install -y php7.2
        # Download Lab files
        wget https://aws-tc-largeobjects.s3-us-west-2.amazonaws.com/ILT-TF-200-ACACAD-20-EN/mod6-guided/scripts/inventory-app.zip
        unzip inventory-app.zip -d /var/www/html/
        # Download and install the AWS SDK for PHP
        wget https://github.com/aws/aws-sdk-php/releases/download/3.62.3/aws.zip
        unzip aws -d /var/www/html
        # Turn on web server
        chkconfig httpd on
        service httpd start
        ```
    - From the Summary section, choose Launch instance
3. Choose View all instances
4. Wait for the application server to fully launch. It should display the following status:
    - Instance State: Running
    - You can choose refresh occasionally to update the display.
5. Select App Server.
6. From the Details tab, copy the Public IPv4 address address.
7. Open a new browser tab, paste the IP address you just copied, and press Enter.
    - If you configured the VPC correctly, the Inventory application and this message should appear: Please configure Settings to connect to database. You have not configured any database settings yet, but the appearance of the Inventory application demonstrates that the public subnet was correctly configured.
    - If the Inventory application does not appear, wait for 60 seconds and refresh the page to try again. It can take a couple of minutes for the EC2 instance to boot and run the script that installs the software.
8. At the top of these instructions, choose Submit to record your progress and when prompted, choose Yes.
