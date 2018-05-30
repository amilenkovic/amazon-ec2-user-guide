# Launching an Instance from a Launch Template<a name="ec2-launch-templates"></a>

You can create a *launch template* that contains the configuration information to launch an instance\. Launch templates enable you to store launch parameters so that you do not have to specify them every time you launch an instance\. For example, a launch template can contain the AMI ID, instance type, and network settings that you typically use to launch instances\. When you launch an instance using the Amazon EC2 console, an AWS SDK, or a command line tool, you can specify the launch template to use\.

For each launch template, you can create one or more numbered *launch template versions*\. Each version can have different launch parameters\. When you launch an instance from a launch template, you can use any version of the launch template\. If you do not specify a version, the default version is used\. You can set any version of the launch template as the default version—by default, it's the first version of the launch template\.

The following diagram shows a launch template with three versions\. The first version specifies the instance type, AMI ID, subnet, and key pair to use to launch the instance\. The second version is based on the first version and also specifies a security group for the instance\. The third version uses different values for some of the parameters\. Version 2 is set as the default version\. If you launched an instance from this launch template, the launch parameters from version 2 would be used if no other version were specified\.

![\[Launch template\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/launch-template-diagram.png)

**Topics**
+ [Launch Template Restrictions](#launch-template-restrictions)
+ [Using Launch Templates to Control Launch Parameters](#launch-templates-authorization)
+ [Controlling the Use of Launch Templates](#launch-template-permissions)
+ [Creating a Launch Template](#create-launch-template)
+ [Managing Launch Template Versions](#manage-launch-template-versions)
+ [Launching an Instance from a Launch Template](#launch-instance-from-launch-template)
+ [Using Launch Templates with Amazon EC2 Auto Scaling](#launch-templates-as)
+ [Using Launch Templates with Spot Fleet](#launch-templates-spot-fleet)
+ [Deleting a Launch Template](#delete-launch-template)

## Launch Template Restrictions<a name="launch-template-restrictions"></a>

The following rules apply to launch templates and launch template versions:
+ You are limited to creating 1,000 launch templates per region and 10,000 versions per launch template\.
+ Launch parameters are optional\. However, you must ensure that your request to launch an instance includes all required parameters\. For example, if your launch template does not include an AMI ID, you must specify both the launch template and an AMI ID when you launch an instance\.
+ Launch template parameters are not validated when you create the launch template\. Ensure that you specify the correct values for the parameters and that you use supported parameter combinations\. For example, to launch an instance in a placement group, you must specify a supported instance type\.
+ You can tag a launch template, but you cannot tag a launch template version\.
+ Launch template versions are numbered in the order in which they are created\. When you create a launch template version, you cannot specify the version number yourself\.

## Using Launch Templates to Control Launch Parameters<a name="launch-templates-authorization"></a>

A launch template can contain all or some of the parameters to launch an instance\. When you launch an instance using a launch template, you can override parameters that are specified in the launch template, or you can specify additional parameters that are not in the launch template\.

**Note**  
You cannot remove launch template parameters during launch \(for example, you cannot specify a null value for the parameter\)\. To remove a parameter, create a new version of the launch template without the parameter and use that version to launch the instance\.

To launch instances, IAM users must have permission to use the `ec2:RunInstances` action, and they must have permission to create or use the resources that are created or associated with the instance\. You can use resource\-level permissions for the `ec2:RunInstances` action to control the launch parameters that users can specify, or you can grant users permission to launch an instance using a launch template instead\. This enables you to manage launch parameters in a launch template rather than in an IAM policy, and to use a launch template as an authorization vehicle for launching instances\. For example, you can specify that users can only launch instances using a launch template, and that they can only use a specific launch template\. You can also control the launch parameters that users can override in the launch template\. For example policies, see [Launch Templates](ExamplePolicies_EC2.md#iam-example-runinstances-launch-templates)\.

## Controlling the Use of Launch Templates<a name="launch-template-permissions"></a>

By default, IAM users do not have permissions to work with launch templates\. You can create an IAM user policy that grants users permissions to create, modify, describe, and delete launch templates and launch template versions\. You can also apply resource\-level permissions to some launch template actions to control a user's ability to use specific resources for those actions\. For more information, see [Supported Resource\-Level Permissions for Amazon EC2 API Actions](ec2-supported-iam-actions-resources.md) and the following example policies: [13\. Working with Launch Templates](ExamplePolicies_EC2.md#iam-example-launch-templates)\.

Take care when granting users permissions to use the `ec2:CreateLaunchTemplate` and `ec2:CreateLaunchTemplateVersion` actions\. These actions do not support resource\-level permissions that enable you to control which resources users can specify in the launch template\. To restrict the resources that are used to launch an instance, ensure that you grant permissions to create launch templates and launch template versions only to appropriate administrators\.

## Creating a Launch Template<a name="create-launch-template"></a>

You can create a new launch template using parameters that you define, or you can use an existing instance as the basis for a new launch template\.

**To create a new launch template**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**, **Create launch template**\.

1. Provide a name and description for the launch template\.

1. For **Launch template contents**, provide the following information\.
   + **AMI ID**: Specify an AMI ID from which to launch the instance\. You can use an AMI that you own, or you can [find a suitable AMI](finding-an-ami.md)\.
   + **Instance type**: Choose the instance type\. Ensure that the instance type is compatible with the AMI you've specified\. For more information, see [Instance Types](instance-types.md)\. 
   + **Key pair name**: Specify the key pair for the instance\. For more information, see [Amazon EC2 Key Pairs](ec2-key-pairs.md)\.
   + **Network type**: If applicable, choose whether to launch the instance into EC2\-Classic or a VPC\. This option is not available if your account supports EC2\-VPC only\. If you choose EC2\-Classic, ensure that the specified instance type is supported in EC2\-Classic and specify the Availability Zone for the instance\. If you choose EC2\-VPC, specify the subnet in the **Network interfaces** section\.

1. For **Network interfaces**, you can specify up to two [network interfaces](using-eni.md) for the instance\.
   + **Device**: Specify the device number for the network interface; for example, `eth0` for the primary network interface\. If you leave the field blank, AWS creates the primary network interface\.
   + **Network interface**: Specify the ID of the network interface or leave blank to let AWS create a new network interface\.
   + **Description**: Optionally enter a description for a new network interface\.
   + **Subnet**: Specify the subnet in which to create a new network interface\. For the primary network interface \(eth0\), this is the subnet in which the instance is launched\. If you've specified an existing network interface for `eth0`, the instance is launched in the subnet in which the network interface is located\.
   + **Auto\-assign public IP**: Specify whether to automatically assign a public IP address to the network interface with the device index of eth0\. This setting can only be enabled for a single, new network interface\.
   + **Primary IP**: Enter a private IPv4 address from the range of your subnet, or leave blank to let AWS choose a private IPv4 address for you\.
   + **Secondary IP**: Enter a secondary private IPv4 address from the range of your subnet, or leave blank to let AWS choose one for you\.
   + \(IPv6\-only\) **IPv6 IPs**: Enter an IPv6 address from the range of the subnet\.
   + **Security group ID**: Enter the ID of a security group in your VPC with which to associate the network interface\.
   + **Delete on termination**: Choose whether the network interface is deleted when the instance is deleted\.

1. For **Storage \(Volumes\)**, specify volumes to attach to the instance besides the volumes specified by the AMI\.
   + **Volume type**: Specify instance store or Amazon EBS volumes with which to associate your instance\. The type of volume depends on the instance type that you've chosen\. For more information, see [Amazon EC2 Instance Store](InstanceStorage.md) and [Amazon EBS Volumes](EBSVolumes.md)\.
   + **Device name**: Specify a device name for the volume\.
   + **Snapshot**: Enter the ID of the snapshot from which to create the volume\.
   + **Size**: For Amazon EBS\-backed volumes, specify a storage size\.
   + **Volume type**: For Amazon EBS volumes, the volume type\. For more information, see [Amazon EBS Volume Types](EBSVolumeTypes.md)\.
   + **IOPS**: If you have selected a Provisioned IOPS SSD volume type, then you can enter the number of I/O operations per second \(IOPS\) that the volume can support\.
   + **Delete on termination**: For Amazon EBS volumes, select this check box to delete the volume when the instance is terminated\. For more information, see [Preserving Amazon EBS Volumes on Instance Termination](terminating-instances.md#preserving-volumes-on-termination)\.
   + **Encrypted**: Select this check box to encrypt new Amazon EBS volumes\. Amazon EBS volumes that are restored from encrypted snapshots are automatically encrypted\. Encrypted volumes may only be attached to [supported instance types](EBSEncryption.md#EBSEncryption_supported_instances)\.

1. For **Tags**, specify [tags](Using_Tags.md) by providing key and value combinations\. You can tag the instance, the volumes, or both\.

1. For **Security groups**, specify one or more security groups to associate with the instance\. For more information, see [Amazon EC2 Security Groups for Linux Instances](using-network-security.md)\.

1. For **Advanced Details**, expand the section to view the fields and specify any additional parameters for the instance\.
   + **IAM instance profile**: Specify an AWS Identity and Access Management \(IAM\) instance profile to associate with the instance\. For more information, see [IAM Roles for Amazon EC2](iam-roles-for-amazon-ec2.md)\.
   + **Shutdown behavior**: Select whether the instance should stop or terminate when shut down\. For more information, see [Changing the Instance Initiated Shutdown Behavior](terminating-instances.md#Using_ChangingInstanceInitiatedShutdownBehavior)\.
   + **Termination protection**: Select whether to prevent accidental termination\. For more information, see [Enabling Termination Protection for an Instance](terminating-instances.md#Using_ChangingDisableAPITermination)\.
   + **Monitoring**: Select whether to enable detailed monitoring of the instance using Amazon CloudWatch\. Additional charges apply\. For more information, see [Monitoring Your Instances Using CloudWatch](using-cloudwatch.md)\.
   + **Placement group name**: Specify a placement group in which to launch the instance\. Not all instance types can be launched in a placement group\. For more information, see [Placement Groups](placement-groups.md)\.
   + **EBS\-optimized instance**: Provides additional, dedicated capacity for Amazon EBS I/O\. Not all instance types support this feature, and additional charges apply\. For more information, see [Amazon EBS–Optimized Instances](EBSOptimized.md)\.
   + **Tenancy**: Specify whether to run your instance on isolated, dedicated hardware \(**Dedicated**\) or on a Dedicated Host \(**Dedicated host**\)\. Additional charges may apply\. For more information, see [Dedicated Instances](dedicated-instance.md) and [Dedicated Hosts](dedicated-hosts-overview.md)\. If you specify a Dedicated Host, you can choose a specific host and the affinity for the instance\.
   + **RAM disk ID**: A RAM disk for the instance\. If you have selected a kernel, you may need to select a specific RAM disk with the drivers to support it\. Only valid for paravirtual \(PV\) AMIs\.
   + **Kernel ID**: A kernel for the instance\. Only valid for paravirtual \(PV\) AMIs\.
   + **User data**: You can specify user data to configure an instance during launch, or to run a configuration script\. For more information, see [Running Commands on Your Linux Instance at Launch](user-data.md)\.

1. Choose **Create launch template**\.

**To create a launch template from an existing launch template**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**, **Create launch template**\.

1. Provide a name and description for the launch template\.

1. For **Source template**, choose a launch template on which to base the new launch template\.

1. For **Source template version**, choose the launch template version on which to base the new launch template\.

1. Adjust any launch parameters as required, and choose **Create launch template**\.

**To create a launch template using the command line**
+ Use the [create\-launch\-template](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-launch-template.html) \(AWS CLI\) command\. The following example creates a launch template that specifies the subnet in which to launch the instance \(`subnet-7b16de0c`\), assigns a public IP address and an IPv6 address to the instance, and creates a tag for the instance \(`Name=webserver`\)\.

  ```
  aws ec2 create-launch-template --launch-template-name TemplateForWebServer --version-description WebVersion1 --launch-template-data '{"NetworkInterfaces":[{"AssociatePublicIpAddress":true,"DeviceIndex":0,"Ipv6AddressCount":1,"SubnetId":"subnet-7b16de0c"}],"ImageId":"ami-8c1be5f6","InstanceType":"t2.small","TagSpecifications":[{"ResourceType":"instance","Tags":[{"Key":"Name","Value":"webserver"}]}]}'
  ```

  ```
  {
      "LaunchTemplate": {
          "LatestVersionNumber": 1, 
          "LaunchTemplateId": "lt-01238c059e3466abc", 
          "LaunchTemplateName": "TemplateForWebServer", 
          "DefaultVersionNumber": 1, 
          "CreatedBy": "arn:aws:iam::123456789012:root", 
          "CreateTime": "2017-11-27T09:13:24.000Z"
      }
  }
  ```

**To get instance data for a launch template using the command line**
+ Use the [get\-launch\-template\-data](http://docs.aws.amazon.com/cli/latest/reference/ec2/get-launch-template-data.html) \(AWS CLI\) command and specify the instance ID\. You can use the output as a base to create a new launch template or launch template version\. By default, the output includes a top\-level `LaunchTemplateData` object, which cannot be specified in your launch template data\. Use the `--query` option to exclude this object\.

  ```
  aws ec2 get-launch-template-data --instance-id i-0123d646e8048babc --query 'LaunchTemplateData'
  ```

  ```
      {
          "Monitoring": {}, 
          "ImageId": "ami-8c1be5f6", 
          "BlockDeviceMappings": [
              {
                  "DeviceName": "/dev/xvda", 
                  "Ebs": {
                      "DeleteOnTermination": true
                  }
              }
          ], 
          "EbsOptimized": false, 
          "Placement": {
              "Tenancy": "default", 
              "GroupName": "", 
              "AvailabilityZone": "us-east-1a"
          }, 
          "InstanceType": "t2.micro", 
          "NetworkInterfaces": [
              {
                  "Description": "", 
                  "NetworkInterfaceId": "eni-35306abc", 
                  "PrivateIpAddresses": [
                      {
                          "Primary": true, 
                          "PrivateIpAddress": "10.0.0.72"
                      }
                  ], 
                  "SubnetId": "subnet-7b16de0c", 
                  "Groups": [
                      "sg-7c227019"
                  ], 
                  "Ipv6Addresses": [
                      {
                          "Ipv6Address": "2001:db8:1234:1a00::123"
                      }
                  ], 
                  "PrivateIpAddress": "10.0.0.72"
              }
          ]
      }
  ```

  You can write the output directly to a file, for example:

  ```
  aws ec2 get-launch-template-data --instance-id i-0123d646e8048babc --query 'LaunchTemplateData' >> instance-data.json
  ```

## Managing Launch Template Versions<a name="manage-launch-template-versions"></a>

You can create launch template versions for a specific launch template, set the default version, and delete versions that you no longer require\.

**Topics**
+ [Creating a Launch Template Version](#create-launch-template-version)
+ [Setting the Default Launch Template Version](#set-default-launch-template-version)
+ [Deleting a Launch Template Version](#delete-launch-template-version)

### Creating a Launch Template Version<a name="create-launch-template-version"></a>

When you create a launch template version, you can specify new launch parameters or use an existing version as the base for the new version\.

**To create a launch template version using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**, and select the launch template\.

1. Choose **Create launch template**, **Create a new template version**\.

1. Specify a description for the launch template version\.

1. To create a launch template version from an existing version, select the source template and source template version\.

1. Specify or adjust the launch parameters as required, and choose **Create launch template**\. For more information about launch template parameters, see [Creating a Launch Template](#create-launch-template)\.

To view information about launch template versions, select the launch template and choose **Versions** in the details pane\. 

**To create a launch template version using the command line**
+ Use the [create\-launch\-template\-version](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-launch-template-version.html) \(AWS CLI\) command\. You can specify a source version on which to base the new version\. The new version inherits the same launch parameters, except for parameters that you specify in `--launch-template-data`\. The following example creates a new version based on version 1 of the launch template and specifies a different AMI ID\.

  ```
  aws ec2 create-launch-template-version --launch-template-id lt-0abcd290751193123 --version-description WebVersion2 --source-version 1 --launch-template-data '{"ImageId":"ami-c998b6b2"}'
  ```

### Setting the Default Launch Template Version<a name="set-default-launch-template-version"></a>

You can set the default version for the launch template\. When you launch an instance from a launch template and do not specify a version, the instance is launched using the parameters of the default version\.

**To set the default launch template version using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates** and select the launch template\.

1. Choose **Actions**, **Set default version**\.

1. For **Default version**, select the version number and choose **Set as default version**\.

**To set the default launch template version using the command line**
+ Use the [modify\-launch\-template](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-launch-template.html) \(AWS CLI\) command and specify the version that you want to set as the default\.

  ```
  aws ec2 modify-launch-template --launch-template-id lt-0abcd290751193123 --default-version 2
  ```

### Deleting a Launch Template Version<a name="delete-launch-template-version"></a>

If you no longer require a launch template version, you can delete it\. You cannot replace the version number after you delete it\. You cannot delete the default version of the launch template; you must first assign a different version as the default\.

**To delete a launch template version using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates** and select the launch template\.

1. Choose **Actions**, **Delete template version**\.

1. Select the version to delete and choose **Delete launch template version**\.
**Note**  
If you've specified the launch template version in an Auto Scaling group or a Spot Fleet request, ensure that you update the Auto Scaling group to use a different version\.

**To delete a launch template version using the command line**
+ Use the [delete\-launch\-template\-versions](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-launch-template-versions.html) \(AWS CLI\) command and specify the version numbers to delete\.

  ```
  aws ec2 delete-launch-template-versions --launch-template-id lt-0abcd290751193123 --versions 1
  ```

## Launching an Instance from a Launch Template<a name="launch-instance-from-launch-template"></a>

You can use the parameters contained in a launch template to launch an instance\. You have the option to override or add launch parameters before you launch the instance\.

Instances that are launched using a launch template are automatically assigned two tags with the keys `aws:ec2launchtemplate:id` and `aws:ec2launchtemplate:version`\. You cannot remove or edit these tags\.

**To launch an instance from a launch template using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates** and select the launch template\.

1. Choose **Actions**, **Launch instance from template**\.

1. Select the launch template version to use\.

1. \(Optional\) You can override or add launch template parameters by changing and adding parameters in the **Instance details** section\.

1. Choose **Launch instance from template**\.

**To launch an instance from a launch template using the command line**
+ Use the [run\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command and specify the `--launch-template` parameter\. Optionally specify the launch template version to use\. If you don't specify the version, the default version is used\.

  ```
  aws ec2 run-instances --launch-template LaunchTemplateId=lt-0abcd290751193123,Version=1
  ```
+ To override a launch template parameter, specify the parameter in the [run\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command\. The following example overrides the instance type that's specified in the launch template \(if any\)\.

  ```
  aws ec2 run-instances --launch-template LaunchTemplateId=lt-0abcd290751193123 --instance-type t2.small
  ```
+ If you specify a nested parameter that's part of a complex structure, the instance is launched using the complex structure as specified in the launch template plus any additional nested parameters that you specify\.

  In the following example, the instance is launched with the tag `Owner=TeamA` as well as any other tags that are specified in the launch template\. If the launch template has an existing tag with a key of `Owner`, the value is replaced with `TeamA`\.

  ```
  aws ec2 run-instances --launch-template LaunchTemplateId=lt-0abcd290751193123 --tag-specifications "ResourceType=instance,Tags=[{Key=Owner,Value=TeamA}]"
  ```

  In the following example, the instance is launched with a volume with the device name `/dev/xvdb` as well as any other block device mappings that are specified in the launch template\. If the launch template has an existing volume defined for `/dev/xvdb`, its values are replaced with specified values\.

  ```
  aws ec2 run-instances --launch-template LaunchTemplateId=lt-0abcd290751193123 --block-device-mappings "DeviceName=/dev/xvdb,Ebs={VolumeSize=20,VolumeType=gp2}"
  ```

## Using Launch Templates with Amazon EC2 Auto Scaling<a name="launch-templates-as"></a>

You can create an Auto Scaling group and specify a launch template to use for the group\. When Amazon EC2 Auto Scaling launches instances in the Auto Scaling group, it uses the launch parameters defined in the associated launch template\.

For more information, see [Creating an Auto Scaling Group Using a Launch Template](http://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-launch-template.html) in the *Amazon EC2 Auto Scaling User Guide*\.

**To create or update an Amazon EC2 Auto Scaling group with a launch template using the command line**
+ Use the [create\-auto\-scaling\-group](http://docs.aws.amazon.com/cli/latest/reference/autoscaling/create-auto-scaling-group.html) or the [update\-auto\-scaling\-group](http://docs.aws.amazon.com/cli/latest/reference/autoscaling/update-auto-scaling-group.html) AWS CLI command and specify the `--launch-template` parameter\.

## Using Launch Templates with Spot Fleet<a name="launch-templates-spot-fleet"></a>

You can create a Spot Fleet request and specify a launch template in the instance configuration\. When Amazon EC2 fulfills the Spot Fleet request, it uses the launch parameters defined in the associated launch template\. You can override some of the parameters that are specified in the launch template\.

For more information, see [Spot Fleet Requests](spot-fleet-requests.md)\.

**To create a Spot Fleet request with a launch template using the command line**
+ Use the [request\-spot\-fleet](http://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) AWS CLI command\. Use the `LaunchTemplateConfigs` parameter to specify the launch template and any overrides for the launch template\.

## Deleting a Launch Template<a name="delete-launch-template"></a>

If you no longer require a launch template, you can delete it\. Deleting a launch template deletes all of its versions\.

**To delete a launch template**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates** and select the launch template\.

1. Choose **Actions**, **Delete template**\.

1. Choose **Delete launch template**\.

**To delete a launch template using the command line**
+ Use the [delete\-launch\-template](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-launch-template.html) \(AWS CLI\) command and specify the launch template\.

  ```
  aws ec2 delete-launch-template --launch-template-id lt-01238c059e3466abc
  ```