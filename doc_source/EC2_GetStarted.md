# Getting Started with Amazon EC2 Linux Instances<a name="EC2_GetStarted"></a>

Let's get started with Amazon Elastic Compute Cloud \(Amazon EC2\) by launching, connecting to, and using a Linux instance\. An *instance* is a virtual server in the AWS cloud\. With Amazon EC2, you can set up and configure the operating system and applications that run on your instance\.

When you sign up for AWS, you can get started with Amazon EC2 for free using the [AWS Free Tier](https://aws.amazon.com/free/)\. If you created your AWS account less than 12 months ago, and have not already exceeded the free tier benefits for Amazon EC2, it will not cost you anything to complete this tutorial, because we help you select options that are within the free tier benefits\. Otherwise, you'll incur the standard Amazon EC2 usage fees from the time that you launch the instance until you terminate the instance \(which is the final task of this tutorial\), even if it remains idle\.

**Topics**
+ [Overview](#ec2-get-started-overview)
+ [Prerequisites](#ec2-getstarted-prereqs)
+ [Step 1: Launch an Instance](#ec2-launch-instance)
+ [Step 2: Connect to Your Instance](#ec2-connect-to-instance-linux)
+ [Step 3: Clean Up Your Instance](#ec2-clean-up-your-instance)
+ [Next Steps](#ec2-next-steps)

## Overview<a name="ec2-get-started-overview"></a>

The instance is an Amazon EBS\-backed instance \(meaning that the root volume is an EBS volume\)\. You can either specify the Availability Zone in which your instance runs, or let Amazon EC2 select an Availability Zone for you\. When you launch your instance, you secure it by specifying a key pair and security group\. When you connect to your instance, you must specify the private key of the key pair that you specified when launching your instance\.

![\[An Amazon EBS-backed instance with an additional Amazon Elastic Block Store (EBS) volume\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/overview_getting_started.png)

**Tasks**

To complete this tutorial, perform the following tasks:

1. [Launch an Instance](#ec2-launch-instance)

1. [Connect to Your Instance](#ec2-connect-to-instance-linux)

1. [Clean Up Your Instance](#ec2-clean-up-your-instance)

**Related Tutorials**
+ If you'd prefer to launch a Windows instance, see this tutorial in the *Amazon EC2 User Guide for Windows Instances*: [Getting Started with Amazon EC2 Windows Instances](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EC2_GetStarted.html)\.
+ If you'd prefer to use the command line, see this tutorial in the *AWS Command Line Interface User Guide*: [Using Amazon EC2 through the AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/cli-using-ec2.html)\.

## Prerequisites<a name="ec2-getstarted-prereqs"></a>

Before you begin, be sure that you've completed the steps in [Setting Up with Amazon EC2](get-set-up-for-amazon-ec2.md)\.

## Step 1: Launch an Instance<a name="ec2-launch-instance"></a>

You can launch a Linux instance using the AWS Management Console as described in the following procedure\. This tutorial is intended to help you launch your first instance quickly, so it doesn't cover all possible options\. For more information about the advanced options, see [Launching an Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html)\.

**To launch an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the console dashboard, choose **Launch Instance**\.

1. The **Choose an Amazon Machine Image \(AMI\)** page displays a list of basic configurations, called *Amazon Machine Images \(AMIs\)*, that serve as templates for your instance\. Select the HVM edition of the Amazon Linux AMI or the AMI for Amazon Linux 2\. Notice that these AMIs are marked "Free tier eligible\."

1. On the **Choose an Instance Type** page, you can select the hardware configuration of your instance\. Select the `t2.micro` type, which is selected by default\. Notice that this instance type is eligible for the free tier\.
**Note**  
[T2 instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/t2-instances.html), such as `t2.micro`, must be launched into a VPC\. If your AWS account supports EC2\-Classic and you do not have a VPC in the selected region, the launch wizard creates a VPC for you and you can continue to the next step\. Otherwise, the **Review and Launch** button is disabled and you must choose **Next: Configure Instance Details** and follow the directions to select a subnet\.

1. Choose **Review and Launch** to let the wizard complete the other configuration settings for you\.

1. On the **Review Instance Launch** page, under **Security Groups**, you'll see that the wizard created and selected a security group for you\. You can use this security group, or alternatively you can select the security group that you created when getting set up using the following steps:

   1. Choose **Edit security groups**\.

   1. On the **Configure Security Group** page, ensure that **Select an existing security group** is selected\.

   1. Select your security group from the list of existing security groups, and then choose **Review and Launch**\.

1. On the **Review Instance Launch** page, choose **Launch**\.

1. When prompted for a key pair, select **Choose an existing key pair**, then select the key pair that you created when getting set up\.

   Alternatively, you can create a new key pair\. Select **Create a new key pair**, enter a name for the key pair, and then choose **Download Key Pair**\. This is the only chance for you to save the private key file, so be sure to download it\. Save the private key file in a safe place\. You'll need to provide the name of your key pair when you launch an instance and the corresponding private key each time you connect to the instance\.
**Warning**  
Don't select the **Proceed without a key pair** option\. If you launch your instance without a key pair, then you can't connect to it\.

   When you are ready, select the acknowledgement check box, and then choose **Launch Instances**\. 

1. A confirmation page lets you know that your instance is launching\. Choose **View Instances** to close the confirmation page and return to the console\.

1. On the **Instances** screen, you can view the status of the launch\. It takes a short time for an instance to launch\. When you launch an instance, its initial state is `pending`\. After the instance starts, its state changes to `running` and it receives a public DNS name\. \(If the **Public DNS \(IPv4\)** column is hidden, choose **Show/Hide Columns** \(the gear\-shaped icon\) in the top right corner of the page and then select **Public DNS \(IPv4\)**\.\)

1. It can take a few minutes for the instance to be ready so that you can connect to it\. Check that your instance has passed its status checks; you can view this information in the **Status Checks** column\.

## Step 2: Connect to Your Instance<a name="ec2-connect-to-instance-linux"></a>

There are several ways to connect to a Linux instance\. In this procedure, you'll connect using your browser\. Alternatively, you can connect using PuTTY or an SSH client\. It's also assumed that you followed the steps earlier and launched an instance from an Amazon Linux AMI, which has a specific user name\. Other Linux distributions may use a different user name\. For more information, see [Connecting to Your Linux Instance from Windows Using PuTTY](putty.md) or [Connecting to Your Linux Instance Using SSH](AccessingInstancesLinux.md)\. 

**Important**  
You can't connect to your instance unless you launched it with a key pair for which you have the \.pem file and you launched it with a security group that allows SSH access\. If you can't connect to your instance, see [Troubleshooting Connecting to Your Instance](TroubleshootingInstancesConnecting.md) for assistance\.

**To connect to your Linux instance using a web browser**

1. You must have Java installed and enabled in the browser\. If you don't have Java already, you can contact your system administrator to get it installed, or follow the steps outlined in the following pages: [Install Java](http://java.com/en/download/help/index_installing.xml) and [Enable Java in your web browser](http://java.com/en/download/help/enable_browser.xml)\.

1. From the Amazon EC2 console, choose **Instances** in the navigation pane\.

1. Select the instance, and then choose **Connect**\.

1. Choose **A Java SSH client directly from my browser \(Java required\)**\.

1. Amazon EC2 automatically detects the public DNS name of your instance and populates **Public DNS** for you\. It also detects the key pair that you specified when you launched the instance\. Complete the following, and then choose **Launch SSH Client**\.

   1. In **User name**, enter `ec2-user`\. 

   1. In **Private key path**, enter the fully qualified path to your private key \(`.pem`\) file, including the key pair name\.

   1. \(Optional\) Choose **Store in browser cache** to store the location of the private key in your browser cache\. This enables Amazon EC2 to detect the location of the private key in subsequent browser sessions, until you clear your browser's cache\.

1. If necessary, choose **Yes** to trust the certificate, and choose **Run** to run the MindTerm client\.

1. If this is your first time running MindTerm, a series of dialog boxes asks you to accept the license agreement, confirm setup for your home directory, and confirm setup of the known hosts directory\. Confirm these settings\.

1. A dialog prompts you to add the host to your set of known hosts\. If you do not want to store the host key information on your local computer, choose **No**\.

   A window opens and you are connected to your instance\.
**Note**  
If you chose **No** in the previous step, you'll see the following message, which is expected:  

   ```
   Verification of server key disabled in this session.
   ```

## Step 3: Clean Up Your Instance<a name="ec2-clean-up-your-instance"></a>

After you've finished with the instance that you created for this tutorial, you should clean up by terminating the instance\. If you want to do more with this instance before you clean up, see [Next Steps](#ec2-next-steps)\.

**Important**  
Terminating an instance effectively deletes it; you can't reconnect to an instance after you've terminated it\.

If you launched an instance that is not within the [AWS Free Tier](https://aws.amazon.com/free/), you'll stop incurring charges for that instance as soon as the instance status changes to `shutting down` or `terminated`\. If you'd like to keep your instance for later, but not incur charges, you can stop the instance now and then start it again later\. For more information, see [Stopping Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Stop_Start.html)\.

**To terminate your instance**

1. In the navigation pane, choose **Instances**\. In the list of instances, select the instance\.

1. Choose **Actions**, **Instance State**, **Terminate**\.

1. Choose **Yes, Terminate** when prompted for confirmation\.

   Amazon EC2 shuts down and terminates your instance\. After your instance is terminated, it remains visible on the console for a short while, and then the entry is deleted\.

## Next Steps<a name="ec2-next-steps"></a>

After you start your instance, you might want to try some of the following exercises:
+ Learn how to remotely manage your EC2 instance using Run Command\. For more information, see [Tutorial: Remotely Manage Your Amazon EC2 Instances](tutorial_run_command.md) and [Systems Manager Remote Management \(Run Command\)](http://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html)\.
+ Configure a CloudWatch alarm to notify you if your usage exceeds the Free Tier\. For more information, see [Create a Billing Alarm](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/free-tier-alarms.html) in the *AWS Billing and Cost Management User Guide*\.
+ Add an EBS volume\. For more information, see [Creating an Amazon EBS Volume](ebs-creating-volume.md) and [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)\.
+ Install the LAMP stack\. For more information, see [Tutorial: Install a LAMP Web Server with the Amazon Linux AMI](install-LAMP.md)\.