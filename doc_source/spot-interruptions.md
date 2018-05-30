# Spot Instance Interruptions<a name="spot-interruptions"></a>

Demand for Spot Instances can vary significantly from moment to moment, and the availability of Spot Instances can also vary significantly depending on how many unused EC2 instances are available\. It is always possible that your Spot Instance will be interrupted\. Therefore, you must ensure that your application is prepared for a Spot Instance interruption\.

The following are the possible reasons that Amazon EC2 will interrupt your Spot Instances:
+ Price – The Spot price is greater than your maximum price\.
+ Capacity – If there are not enough unused EC2 instances to meet the demand for Spot Instances, Amazon EC2 interrupts Spot Instances\. The order in which the instances are interrupted is determined by Amazon EC2\.
+ Constraints – If your request includes a constraint such as a launch group or an Availability Zone group, these Spot Instances are terminated as a group when the constraint can no longer be met\.

An On\-Demand Instance specified in a Spot Fleet cannot be interrupted\.

## Interruption Behavior<a name="interruption-behavior"></a>

You can specify whether Amazon EC2 should hibernate, stop, or terminate Spot Instances when they are interrupted\. You can choose the interruption behavior that meets your needs\. The default is to terminate Spot Instances when they are interrupted\. To change the interruption behavior, choose an option from **Interruption behavior** in the console or `InstanceInterruptionBehavior` in the launch configuration or the launch template\.

### Stopping Interrupted Spot Instances<a name="stop-spot-instances"></a>

You can change the behavior so that Amazon EC2 stops Spot Instances when they are interrupted if the following requirements are met\.

**Requirements**
+ For a Spot Instance request, the type must be `persistent`, not `one-time`\. You cannot specify a launch group in the Spot Instance request\.
+ For a Spot Fleet request, the type must be `maintain`, not `request`\.
+ The root volume must be an EBS volume, not an instance store volume\.

After a Spot Instance is stopped by the Spot service, it can only be restarted by the Spot service, and only using the same launch configuration\. When capacity is available that matches the Availability Zone and instance type of a Spot Instance that is stopped, the Spot Instance is started\. With Spot Fleet, if capacity is available only with a different Availability Zone or instance type, Spot Fleet launches a new Spot Instance using the launch configuration with available capacity\.

While a Spot Instance is stopped, you can modify some of its instance attributes, but not the instance type\. If you detach or delete an EBS volume, it is not attached when the Spot Instance is started\. If you detach the root volume and the Spot service attempts to start the Spot Instance, instance start fails and the Spot service terminates the stopped instance\.

You can terminate a Spot Instance while it is stopped\. If you cancel a Spot request or a Spot Fleet, the Spot service terminates any associated Spot Instances that are stopped\.

While a Spot Instance is stopped, you are charged only for the EBS volumes, which are preserved\. With Spot Fleet, if you have many stopped instances, you can exceed the limit on the number of EBS volumes for your account\.

### Hibernating Interrupted Spot Instances<a name="hibernate-spot-instances"></a>

You can change the behavior so that Amazon EC2 hibernates Spot Instances when they are interrupted if the following requirements are met\.

**Requirements**
+ For a Spot Instance request, the type must be `persistent`, not `one-time`\. You cannot specify a launch group in the Spot Instance request\.
+ For a Spot Fleet request, the type must be `maintain`, not `request`\.
+ The root volume must be an EBS volume, not an instance store volume, and it must be large enough to store the instance memory \(RAM\) during hibernation\.
+ The following instances are supported: C3, C4, C5, M4, M5, R3, and R4, with less than 100 GB of memory\.
+ The following operating systems are supported: Amazon Linux AMI, Ubuntu with an AWS\-tuned Ubuntu kernel \(linux\-aws\) greater than 4\.4\.0\-1041, and Windows Server 2008 R2 and later\.
+ Install the hibernation agent on a supported operating system, or use one of the following AMIs, which already include the agent:
  + Amazon Linux AMI 2017\.09\.1 or later
  + Ubuntu Xenial 16\.04 20171121 or later
  + Windows Server 2008 R2 AMI 2017\.11\.19 or later
  + Windows Server 2012 or Windows Server 2012 R2 AMI 2017\.11\.19 or later
  + Windows Server 2016 AMI 2017\.11\.19 or later
+ Start the agent\. We recommend that you use user data to start the agent on instance startup\. Alternatively, you could start the agent manually\.

**Recommendation**
+ We strongly recommend that you use an encrypted EBS volume as the root volume, because instance memory is stored on the root volume during hibernation\. This ensures that the contents of memory \(RAM\) are encrypted when the data is at rest on the volume and when data is moving between the instance and volume\. If your AMI does not have an encrypted root volume, you can copy it to a new AMI and request encryption\. For more information, see [Amazon EBS Encryption](EBSEncryption.md) and [Copying an AMI](CopyingAMIs.md#ami-copy-steps)\.

When a Spot Instance is hibernated by the Spot service, the EBS volumes are preserved and instance memory \(RAM\) is preserved on the root volume\. The private IP addresses of the instance are also preserved\. Instance storage volumes and public IP addresses, other than Elastic IP addresses, are not preserved\. While the instance is hibernating, you are charged only for the EBS volumes\. With Spot Fleet, if you have many hibernated instances, you can exceed the limit on the number of EBS volumes for your account\.

The agent prompts the operating system to hibernate when the instance receives a signal from the Spot service\. If the agent is not installed, the underlying operating system doesn't support hibernation, or there isn't enough volume space to save the instance memory, hibernation fails and the Spot service stops the instance instead\.

When the Spot service hibernates a Spot Instance, you receive an interruption notice, but you do not have two minutes before the Spot Instance is interrupted\. Hibernation begins immediately\. While the instance is in the process of hibernating, instance health checks might fail\. When the hibernation process completes, the state of the instance is `stopped`\.

After a Spot Instance is hibernated by the Spot service, it can only be resumed by the Spot service\. The Spot service resumes the instance when capacity becomes available with a Spot price that is less than your specified maximum price\.

For more information, see [Preparing for Instance Hibernation](#prepare-for-instance-hibernation)\.

## Preparing for Interruptions<a name="using-spot-instances-managing-interruptions"></a>

Here are some best practices to follow when you use Spot Instances:
+ Use the default maximum price, which is the On\-Demand price\.
+ Ensure that your instance is ready to go as soon as the request is fulfilled by using an Amazon Machine Image \(AMI\) that contains the required software configuration\. You can also use user data to run commands at start\-up\.
+ Store important data regularly in a place that won't be affected when the Spot Instance terminates\. For example, you can use Amazon S3, Amazon EBS, or DynamoDB\.
+ Divide the work into small tasks \(using a Grid, Hadoop, or queue\-based architecture\) or use checkpoints so that you can save your work frequently\.
+ Use Spot Instance interruption notices to monitor the status of your Spot Instances\.
+ While we make every effort to provide this warning as soon as possible, it is possible that your Spot Instance will be terminated before the warning can be made available\. Test your application to ensure that it handles an unexpected instance termination gracefully, even if you are testing for interruption notices\. You can do so by running the application using an On\-Demand Instance and then terminating the On\-Demand Instance yourself\.

## Preparing for Instance Hibernation<a name="prepare-for-instance-hibernation"></a>

You must install a hibernation agent on your instance, unless you used an AMI that already includes the agent\. You must run the agent on instance startup, whether the agent was included in your AMI or you installed it yourself\.

The following procedures help you prepare a Linux instance\. For directions to prepare a Windows instance, see [Preparing for Instance Hibernation](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/spot-interruptions.html#prepare-for-instance-hibernation) in the *Amazon EC2 User Guide for Windows Instances*\.

**To prepare an Amazon Linux instance**

1. Verify that your kernel supports hibernation and update the kernel if necessary\.

1. If your AMI doesn't include the agent, install the agent using the following command:

   ```
   sudo yum update; sudo yum install hibagent
   ```

1. Add the following to user data:

   ```
   #!/bin/bash
   /usr/bin/enable-ec2-spot-hibernation
   ```

**To prepare an Ubuntu instance**

1. If your AMI doesn't include the agent, install the agent using the following command:

   ```
   sudo apt-get install hibagent
   ```

1. Add the following to user data:

   ```
   #!/bin/bash
   /usr/bin/enable-ec2-spot-hibernation
   ```

## Spot Instance Interruption Notices<a name="spot-instance-termination-notices"></a>

The best way to protect against Spot Instance interruption is to architect your application to be fault tolerant\. In addition, you can take advantage of *Spot Instance interruption notices*, which provide a two\-minute warning before Amazon EC2 must interrupt your Spot Instance\. We recommend that you check for these warnings every 5 seconds\.

This warning is made available as a CloudWatch event and as item in the [instance metadata](ec2-instance-metadata.md) on the Spot Instance\.

### EC2 Spot Instance Interruption Warning<a name="ec2-spot-instance-interruption-warning-event"></a>

When Amazon EC2 interrupts your Spot Instance, it emits an event that can be detected by Amazon CloudWatch Events\. For more information, see the [Amazon CloudWatch Events User Guide](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\.

The following is an example of the event for Spot Instance interruption\. The possible values for `instance-action` are `hibernate`, `stop`, and `terminate`\.

```
{
    "version": "0",
    "id": "12345678-1234-1234-1234-123456789012",
    "detail-type": "EC2 Spot Instance Interruption Warning",
    "source": "aws.ec2",
    "account": "123456789012",
    "time": "yyyy-mm-ddThh:mm:ssZ",
    "region": "us-east-2",
    "resources": ["arn:aws:ec2:us-east-2:123456789012:instance/i-1234567890abcdef0"],
    "detail": {
        "instance-id": "i-1234567890abcdef0",
        "instance-action": "action"
    }
}
```

### instance\-action<a name="instance-action-metadata"></a>

If your Spot Instance is marked to be hibernated, stopped, or terminated by the Spot service, the `instance-action` item is present in your instance metadata\. You can retrieve `instance-action` as follows\.

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/spot/instance-action
```

The `instance-action` item specifies the action and the approximate time, in UTC, when the action will occur\. The following example indicates the time that this instance will be stopped:

```
{"action": "stop", "time": "2017-09-18T08:22:00Z"}
```

The following example indicates the time that this instance will be terminated:

```
{"action": "terminate", "time": "2017-09-18T08:22:00Z"}
```

The following example indicates that hibernation has started immediately:

```
{"action": "hibernate", "time": "2017-11-28T08:22:00Z"}
```

### termination\-time<a name="termination-time-metadata"></a>

If your Spot Instance is marked for termination by the Spot service, the `termination-time` item is present in your instance metadata\. This item is maintained for backward compatibility; you should use `instance-action` instead\. You can retrieve `termination-time` as follows\.

```
[ec2-user ~]$ if curl -s http://169.254.169.254/latest/meta-data/spot/termination-time | grep -q .*T.*Z; then echo terminated; fi
```

The `termination-time` item specifies the approximate time in UTC when the instance will receive the shutdown signal\. For example:

```
2015-01-05T18:02:00Z
```

If Amazon EC2 is not preparing to terminate the instance, or if you terminated the Spot Instance yourself, the `termination-time` item is either not present \(so you receive an HTTP 404 error\) or contains a value that is not a time value\.

If Amazon EC2 fails to terminate the instance, the request status is set to `fulfilled`\. Note that `termination-time` remains in the instance metadata with the original approximate time, which is now in the past\.