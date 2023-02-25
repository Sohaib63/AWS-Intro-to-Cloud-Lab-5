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
