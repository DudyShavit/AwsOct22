# Hello World EC2

### Before you begin  

You need to have an AWS account with web console access. Our shared AWS account is:
<a href="https://964849360084.signin.aws.amazon.com/console" target="_blank">https://488587609044.signin.aws.amazon.com/console</a>. Login with your full mail as username.

## TL;DR

1. Create an EC2 instance, as follows:
   1. `Microsoft Windows Server 2019` AMI.
   2. `t2.medium` instance type  (or equivalent medium type from another generation).
   3. Choose your key-pair (create if needed).
   4. All other configurations are  default. 
2. Your instance should have a public ip4v address. Connect to your instance via RDP by click on **Connect** button in the instance summary page, then RDP Client. 
3. Configure IIS web server and serve simple Hello World page (make sure the appropriate port is open in the instance’s security group).

---

## Launch an instance

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the EC2 console dashboard, in the **Launch instance** box, choose **Launch instance**, and then choose **Launch instance** from the options that appear\.

1. Under **Name and tags**, for **Name**, enter a descriptive name for your instance (that can be identifiable by the group)\.

1. Under **Application and OS Images \(Amazon Machine Image\)**, do the following:

   1. Choose **Quick Start**, and then choose Windows\. This is the operating system \(OS\) for your instance\.

   1. From **Amazon Machine Image \(AMI\)**, select the AMI for Windows Server 2019\.

1. Under **Instance type**, from the **Instance type** list, Choose the `t2.medium` instance type (or equivalent machine). 

1. Under **Key pair \(login\)**, for **Key pair name**, choose the key pair that you created, or create a new one.

1. Keep the default selections for the other configuration settings for your instance\.

1. Review a summary of your instance configuration in the **Summary** panel, and when you're ready, choose **Launch instance**\.

1. On the **Instances** screen, you can view the status of the launch\. It takes a short time for an instance to launch\. When you launch an instance, its initial state is `pending`\. After the instance starts, its state changes to `running` and it receives a public DNS name\.

1. It can take a few minutes for the instance to be ready for you to connect to it\. Check that your instance has passed its status checks; you can view this information in the **Status check** column\.

## Connect to your instance

1. In the navigation pane, select **Instances**\. Select the instance and then choose **Connect**\.

1. On the **Connect to instance** page, choose the **RDP client** tab, and then choose **Get password**\.  
   ![\[Get password for RDP.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-connect-get-password.png)

1. Choose **Browse** and navigate to the private key \(`.pem`\) file you created when you launched the instance\. Select the file and choose **Open** to copy the entire contents of the file to this window\.

1. Choose **Decrypt Password**\. The console displays the default administrator password for the instance under **Password**, replacing the **Get password** link shown previously\. Save the password in a safe place\. This password is required to connect to the instance\.  
   ![\[Password location for RDP.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-connect-password.png)

1. Choose **Download remote desktop file**\. Your browser prompts you to either open or save the RDP shortcut file\. When you have finished downloading the file, choose **Cancel** to return to the **Instances** page\.
   + If you opened the RDP file, you'll see the **Remote Desktop Connection** dialog box\.
   + If you saved the RDP file, navigate to your downloads directory, and open the RDP file to display the dialog box\.

1. The administrator account is chosen by default\. Copy and paste the password that you saved previously\.

## Install IIS With PowerShell

1. Open PowerShell with administrative privileges and run the Install-WindowsFeature cmdlet as shown below
```
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## Authorize inbound HTTP traffic for your instance

**To add a rule to a security group for inbound HTTP traffic over IPv4**

1. Select your instance and, in bottom half of the screen, choose the **Security** tab\. **Security groups** lists the security groups that are associated with the instance\. **Inbound rules** displays a list of the inbound rules that are in effect for the instance\.

2. For the security group to which you'll add the new rule, choose the security group ID link to open the security group\.

3. On the **Inbound rules** tab, choose **Edit inbound rules**\.

4. On the **Edit inbound rules** page, do the following:

5. Choose **Add rule**\.

6. For **Type**, choose **HTTP**\.

7. For **Source**, choose **0.0.0.0/0**. [Read more](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) about CIDR notation. 
   
8. Choose **Save rules**\.

## Visit your webserver

1. Make sure your IIS webserver is accessible from its public ip.  
