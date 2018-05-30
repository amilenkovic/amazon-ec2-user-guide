# Recover Your Instance<a name="ec2-instance-recover"></a>

You can create an Amazon CloudWatch alarm that monitors an Amazon EC2 instance and automatically recovers the instance if it becomes impaired due to an underlying hardware failure or a problem that requires AWS involvement to repair\. Terminated instances cannot be recovered\. A recovered instance is identical to the original instance, including the instance ID, private IP addresses, Elastic IP addresses, and all instance metadata\. For more information about using Amazon CloudWatch alarms to recover an instance, see [Create Alarms That Stop, Terminate, Reboot, or Recover an Instance](UsingAlarmActions.md)\. To troubleshoot issues with instance recovery failures, see [Troubleshooting Instance Recovery Failures](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstanceRecovery.html) in the *Amazon EC2 User Guide for Linux Instances*\.

When the `StatusCheckFailed_System` alarm is triggered, and the recover action is initiated, you will be notified by the Amazon SNS topic that you selected when you created the alarm and associated the recover action\. During instance recovery, the instance is migrated during an instance reboot, and any data that is in\-memory is lost\. When the process is complete, information is published to the SNS topic you've configured for the alarm\. Anyone who is subscribed to this SNS topic will receive an email notification that includes the status of the recovery attempt and any further instructions\. You will notice an instance reboot on the recovered instance\.

Examples of problems that cause system status checks to fail include:
+ Loss of network connectivity
+ Loss of system power
+ Software issues on the physical host
+ Hardware issues on the physical host that impact network reachability

The recover action can also be triggered when an instance is scheduled by AWS to stop or retire due to degradation of the underlying hardware\. For more information about scheduled events, see [Scheduled Events for Your Instances](monitoring-instances-status-check_sched.md)\. 

The recover action is supported only on instances with the following characteristics:
+ Use a C3, C4, C5, M3, M4, M5, R3, R4, T2, or X1 instance type
+ Run in a VPC \(not EC2\-Classic\)
+ Use shared tenancy \(the tenancy attribute is set to `default`\)
+ Use EBS volumes only \(do not configure instance store volumes\)\. For more information, see ['Recover this instance' is disabled](https://aws.amazon.com/premiumsupport/knowledge-center/recover-this-instance-cloudwatch-enable/)\.

If your instance has a public IPv4 address, it retains the public IPv4 address after recovery\.