# Amazon EC2 Root Device Volume<a name="RootDeviceStorage"></a>

When you launch an instance, the *root device volume* contains the image used to boot the instance\. When we introduced Amazon EC2, all AMIs were backed by Amazon EC2 instance store, which means the root device for an instance launched from the AMI is an instance store volume created from a template stored in Amazon S3\. After we introduced Amazon EBS, we introduced AMIs that are backed by Amazon EBS\. This means that the root device for an instance launched from the AMI is an Amazon EBS volume created from an Amazon EBS snapshot\.

You can choose between AMIs backed by Amazon EC2 instance store and AMIs backed by Amazon EBS\. We recommend that you use AMIs backed by Amazon EBS, because they launch faster and use persistent storage\.

For more information about the device names Amazon EC2 uses for your root volumes, see [Device Naming on Linux Instances](device_naming.md)\.

**Topics**
+ [Root Device Storage Concepts](#RootDeviceStorageConcepts)
+ [Choosing an AMI by Root Device Type](#choose-an-ami-by-root-device)
+ [Determining the Root Device Type of Your Instance](#display-instance-root-device-type)
+ [Changing the Root Device Volume to Persist](#Using_RootDeviceStorage)

## Root Device Storage Concepts<a name="RootDeviceStorageConcepts"></a>

You can launch an instance from either an instance store\-backed AMI or an Amazon EBS\-backed AMI\. The description of an AMI includes which type of AMI it is; you'll see the root device referred to in some places as either `ebs` \(for Amazon EBS\-backed\) or `instance store` \(for instance store\-backed\)\. This is important because there are significant differences between what you can do with each type of AMI\. For more information about these differences, see [Storage for the Root Device](ComponentsAMIs.md#storage-for-the-root-device)\.

**Instance Store\-backed Instances**  
Instances that use instance stores for the root device automatically have one or more instance store volumes available, with one volume serving as the root device volume\. When an instance is launched, the image that is used to boot the instance is copied to the root volume\. Note that you can optionally use additional instance store volumes, depending on the instance type\.

Any data on the instance store volumes persists as long as the instance is running, but this data is deleted when the instance is terminated \(instance store\-backed instances do not support the **Stop** action\) or if it fails \(such as if an underlying drive has issues\)\.

![\[Root device on an Amazon EC2 instance store-backed instance\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/instance_store_backed_instance.png)

After an instance store\-backed instance fails or terminates, it cannot be restored\. If you plan to use Amazon EC2 instance store\-backed instances, we highly recommend that you distribute the data on your instance stores across multiple Availability Zones\. You should also back up critical data from your instance store volumes to persistent storage on a regular basis\.

For more information, see [Amazon EC2 Instance Store](InstanceStorage.md)\.

**Amazon EBS\-backed Instances**  
Instances that use Amazon EBS for the root device automatically have an Amazon EBS volume attached\. When you launch an Amazon EBS\-backed instance, we create an Amazon EBS volume for each Amazon EBS snapshot referenced by the AMI you use\. You can optionally use other Amazon EBS volumes or instance store volumes, depending on the instance type\.

![\[Root device volume and other Amazon EBS volumes of an Amazon EBS-backed instance\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ebs_backed_instance.png)

An Amazon EBS\-backed instance can be stopped and later restarted without affecting data stored in the attached volumes\. There are various instance– and volume\-related tasks you can do when an Amazon EBS\-backed instance is in a stopped state\. For example, you can modify the properties of the instance, change its size, or update the kernel it is using, or you can attach your root volume to a different running instance for debugging or any other purpose\. 

If an Amazon EBS\-backed instance fails, you can restore your session by following one of these methods:
+ Stop and then start again \(try this method first\)\.
+ Automatically snapshot all relevant volumes and create a new AMI\. For more information, see [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md)\.
+ Attach the volume to the new instance by following these steps:

  1. Create a snapshot of the root volume\.

  1. Register a new AMI using the snapshot\.

  1. Launch a new instance from the new AMI\.

  1. Detach the remaining Amazon EBS volumes from the old instance\.

  1. Reattach the Amazon EBS volumes to the new instance\.

For more information, see [Amazon EBS Volumes](EBSVolumes.md)\.

## Choosing an AMI by Root Device Type<a name="choose-an-ami-by-root-device"></a>

The AMI that you specify when you launch your instance determines the type of root device volume that your instance has\.

**To choose an Amazon EBS\-backed AMI using the console**

1. Open the Amazon EC2 console\.

1. In the navigation pane, choose **AMIs**\.

1. From the filter lists, select the image type \(such as **Public images**\)\. In the search bar choose **Platform** to select the operating system \(such as **Amazon Linux**\), and **Root Device Type** to select **EBS images**\.

1. \(Optional\) To get additional information to help you make your choice, choose the **Show/Hide Columns** icon, update the columns to display, and choose **Close**\.

1. Choose an AMI and write down its AMI ID\.

**To choose an instance store\-backed AMI using the console**

1. Open the Amazon EC2 console\.

1. In the navigation pane, choose **AMIs**\.

1. From the filter lists, select the image type \(such as **Public images**\)\. In the search bar, choose **Platform** to select the operating system \(such as **Amazon Linux**\), and **Root Device Type** to select **Instance store**\.

1. \(Optional\) To get additional information to help you make your choice, choose the **Show/Hide Columns** icon, update the columns to display, and choose **Close**\.

1. Choose an AMI and write down its AMI ID\.

**To verify the type of the root device volume of an AMI using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-images](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) \(AWS CLI\)
+ [Get\-EC2Image](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-ec2-get-amis.html#pstools-ec2-get-image) \(AWS Tools for Windows PowerShell\)

## Determining the Root Device Type of Your Instance<a name="display-instance-root-device-type"></a>

**To determine the root device type of an instance using the console**

1. Open the Amazon EC2 console\.

1. In the navigation pane, choose **Instances**, and select the instance\.

1. Check the value of **Root device type** in the **Description** tab as follows:
   + If the value is `ebs`, this is an Amazon EBS\-backed instance\.
   + If the value is `instance store`, this is an instance store\-backed instance\.

**To determine the root device type of an instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)
+ [Get\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

## Changing the Root Device Volume to Persist<a name="Using_RootDeviceStorage"></a>

By default, the root device volume for an AMI backed by Amazon EBS is deleted when the instance terminates\. To change the default behavior, set the `DeleteOnTermination` attribute to `false` using a block device mapping\.

### Changing the Root Volume to Persist Using the Console<a name="Using_ChangeRootDeviceVolumeToPersist"></a>

Using the console, you can change the `DeleteOnTermination` attribute when you launch an instance\. To change this attribute for a running instance, you must use the command line\.

**To change the root device volume of an instance to persist at launch using the console**

1. Open the Amazon EC2 console\.

1. From the Amazon EC2 console dashboard, choose **Launch Instance**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, select the AMI to use and choose **Select**\.

1. Follow the wizard to complete the **Choose an Instance Type** and **Configure Instance Details** pages\.

1. On the **Add Storage** page, deselect **Delete On Termination** for the root volume\.

1. Complete the remaining wizard pages, and then choose **Launch**\.

You can verify the setting by viewing details for the root device volume on the instance's details pane\. Next to **Block devices**, choose the entry for the root device volume\. By default, **Delete on termination** is `True`\. If you change the default behavior, **Delete on termination** is `False`\.

### Changing the Root Volume of an Instance to Persist Using the AWS CLI<a name="set-deleteOnTermination-aws-cli"></a>

Using the AWS CLI, you can change the `DeleteOnTermination` attribute when you launch an instance or while the instance is running\.

**Example at Launch**  
Use the [run\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to preserve the root volume by including a block device mapping that sets its `DeleteOnTermination` attribute to `false`\.  

```
aws ec2 run-instances --block-device-mappings file://mapping.json other parameters...
```
Specify the following in `mapping.json`\.  

```
[
  {
    "DeviceName": "/dev/sda1",
    "Ebs": {
        "DeleteOnTermination": false
    }
  }
]
```

You can confirm that `DeleteOnTermination` is `false` by using the [describe\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command and looking for the `BlockDeviceMappings` entry for the device in the command output, as shown here\.

```
...
  "BlockDeviceMappings": [
    {
        "DeviceName": "/dev/sda1",
        "Ebs": {
            "Status": "attached",
            "DeleteOnTermination": false,
            "VolumeId": "vol-1234567890abcdef0",
            "AttachTime": "2013-07-19T02:42:39.000Z"
        }
    }              
...
```

**Example While the Instance is Running**  
Use the [modify\-instance\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) command to preserve the root volume by including a block device mapping that sets its `DeleteOnTermination` attribute to `false`\.  

```
aws ec2 modify-instance-attribute --instance-id i-1234567890abcdef0 --block-device-mappings file://mapping.json
```
Specify the following in `mapping.json`\.  

```
[
  {
    "DeviceName": "/dev/sda1",
    "Ebs" : {
        "DeleteOnTermination": false
    }
  }
]
```