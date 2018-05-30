# Troubleshooting Terminating \(Shutting Down\) Your Instance<a name="TroubleshootingInstancesShuttingDown"></a>

You are not billed for any instance usage while an instance is not in the `running` state\. In other words, when you terminate an instance, you stop incurring charges for that instance as soon as its state changes to `shutting-down`\.

## Delayed Instance Termination<a name="instance-stuck-terminating"></a>

If your instance remains in the `shutting-down` state longer than a few minutes, it might be delayed due to shutdown scripts being run by the instance\.

Another possible cause is a problem with the underlying host computer\. If your instance remains in the `shutting-down` state for several hours, Amazon EC2 treats it as a stuck instance and forcibly terminates it\.

If it appears that your instance is stuck terminating and it has been longer than several hours, post a request for help to the [Amazon EC2 forum](https://forums.aws.amazon.com/forum.jspa?forumID=30)\. To help expedite a resolution, include the instance ID and describe the steps that you've already taken\. Alternatively, if you have a support plan, create a technical support case in the [Support Center](https://console.aws.amazon.com/support/home#/)\.

## Terminated Instance Still Displayed<a name="terminated-instance-still-displaying"></a>

After you terminate an instance, it remains visible for a short while before being deleted\. The state shows as `terminated`\. If the entry is not deleted after several hours, contact Support\.

## Automatically Launch or Terminate Instances<a name="automatic-instance-create-or-delete"></a>

If you terminate all your instances, you may see that we launch a new instance for you\. If you launch an instance, you may see that we terminate one of your instances\. If you stop an instance, you may see that we terminate the instance and launch a new instance\. Generally, these behaviors mean that you've used Amazon EC2 Auto Scaling or Elastic Beanstalk to scale your computing resources automatically based on criteria that you've defined\.

For more information, see the [Amazon EC2 Auto Scaling User Guide](http://docs.aws.amazon.com/autoscaling/latest/userguide/) or the [AWS Elastic Beanstalk Developer Guide](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/)\.