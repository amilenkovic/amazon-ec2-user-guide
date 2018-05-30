# Spot Request Status<a name="spot-bid-status"></a>

To help you track your Spot Instance requests and plan your use of Spot Instances, use the request status provided by Amazon EC2\. For example, the request status can provide the reason why your Spot request isn't fulfilled yet, or list the constraints that are preventing the fulfillment of your Spot request\.

At each step of the process—also called the Spot request *lifecycle*, specific events determine successive request states\.

**Topics**
+ [Life Cycle of a Spot Request](#spot-instances-bid-status-lifecycle)
+ [Getting Request Status Information](#get-spot-instance-bid-status)
+ [Spot Request Status Codes](#spot-instance-bid-status-understand)

## Life Cycle of a Spot Request<a name="spot-instances-bid-status-lifecycle"></a>

The following diagram shows you the paths that your Spot request can follow throughout its lifecycle, from submission to termination\. Each step is depicted as a node, and the status code for each node describes the status of the Spot request and Spot Instance\.

![\[Life cycle of a Spot request\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/spot-bid-status-diagram.png)

**Pending evaluation**  
As soon as you make a Spot Instance request, it goes into the `pending-evaluation` state unless one or more request parameters is not valid \(`bad-parameters`\)\.


| Status Code | Request State | Instance State | 
| --- | --- | --- | 
|  `pending-evaluation`  |  `open`  |  n/a  | 
|  `bad-parameters`  |  `closed`  |  n/a  | 

**Holding**  
If one or more request constraints are valid but can't be met yet, or if there is not enough capacity, the request goes into a holding state waiting for the constraints to be met\. The request options affect the likelihood of the request being fulfilled\. For example, if you specify a maximum price below the current Spot price, your request stays in a holding state until the Spot price goes below your maximum price\. If you specify an Availability Zone group, the request stays in a holding state until the Availability Zone constraint is met\.


| Status Code | Request State | Instance State | 
| --- | --- | --- | 
|  `capacity-not-available`  |  `open`  |  n/a  | 
|  `capacity-oversubscribed`  |  `open`  |  n/a  | 
|  `price-too-low`  |  `open`  |  n/a  | 
|  `not-scheduled-yet`  |  `open`  |  n/a  | 
|  `launch-group-constraint`  |  `open`  |  n/a  | 
|  `az-group-constraint`  |  `open`  |  n/a  | 
|  `placement-group-constraint`  |  `open`  |  n/a  | 
|  `constraint-not-fulfillable`  |  `open`  |  n/a  | 

**Pending evaluation/fulfillment\-terminal**  
Your Spot Instance request can go to a `terminal` state if you create a request that is valid only during a specific time period and this time period expires before your request reaches the pending fulfillment phase, you cancel the request, or a system error occurs\.


| Status Code | Request State | Instance State | 
| --- | --- | --- | 
|  `schedule-expired`  |  `cancelled`  |  n/a  | 
|  `canceled-before-fulfillment`\*  |  `cancelled`  |  n/a  | 
|  `bad-parameters`  |  `failed`  |  n/a  | 
|  `system-error`  |  `closed`  |  n/a  | 

\* If you cancel the request\.

**Pending fulfillment**  
When the constraints you specified \(if any\) are met and your maximum price is equal to or higher than the current Spot price, your Spot request goes into the `pending-fulfillment` state\.

At this point, Amazon EC2 is getting ready to provision the instances that you requested\. If the process stops at this point, it is likely to be because it was cancelled by the user before a Spot Instance was launched, or because an unexpected system error occurred\.


| Status Code | Request State | Instance State | 
| --- | --- | --- | 
|  `pending-fulfillment`  |  `open`  |  n/a  | 

**Fulfilled**  
When all the specifications for your Spot Instances are met, your Spot request is fulfilled\. Amazon EC2 launches the Spot Instances, which can take a few minutes\. If a Spot Instance is hibernated or stopped when interrupted, it remains in this state until the request can be fulfilled again or the request is cancelled\.


| Status Code | Request State | Instance State | 
| --- | --- | --- | 
|  `fulfilled`  |  `active`  |  `pending` → `running`  | 
|  `fulfilled`  |  `active`  |  `stopped` → `running`  | 

**Fulfilled\-terminal**  
Your Spot Instances continue to run as long as your maximum price is at or above the Spot price, there is available capacity for your instance type, and you don't terminate the instance\. If a change in the Spot price or available capacity requires Amazon EC2 to terminate your Spot Instances, the Spot request goes into a terminal state\. For example, if your price equals the Spot price but Spot Instances are not available, the status code is `instance-terminated-capacity-oversubscribed`\. A request also goes into the terminal state if you cancel the Spot request or terminate the Spot Instances\.


| Status Code | Request State | Instance State | 
| --- | --- | --- | 
|  `request-canceled-and-instance-running`  |  `cancelled`  |  `running`  | 
|  `marked-for-stop`  |  `active`  |  `running`  | 
|  `marked-for-termination`  |  `closed`  |  `running`  | 
|  `instance-terminated-by-price`  |  `closed` \(one\-time\), `open` \(persistent\)  |  `terminated`  | 
|  `instance-terminated-by-service`  |  `cancelled`  |  `terminated`  | 
|  `instance-terminated-by-user`  |  `closed` or `cancelled` \*  |  `terminated`  | 
|  `instance-terminated-no-capacity`  |  `closed` \(one\-time\), `open` \(persistent\)  |  `terminated`  | 
|  `instance-terminated-capacity-oversubscribed`  |  `closed` \(one\-time\), `open` \(persistent\)  |  `terminated`  | 
|  `instance-terminated-launch-group-constraint`  |  `closed` \(one\-time\), `open` \(persistent\)  |  `terminated`  | 

\* The request state is `closed` if you terminate the instance but do not cancel the request\. The request state is `cancelled` if you terminate the instance and cancel the request\. Note that even if you terminate a Spot Instance before you cancel its request, there might be a delay before Amazon EC2 detects that your Spot Instance was terminated\. In this case, the request state can either be `closed` or `cancelled`\.

**Persistent requests**  
When your Spot Instances are terminated \(either by you or Amazon EC2\), if the Spot request is a persistent request, it returns to the `pending-evaluation` state and then Amazon EC2 can launch a new Spot Instance when the constraints are met\.

## Getting Request Status Information<a name="get-spot-instance-bid-status"></a>

You can get request status information using the AWS Management Console or a command line tool\.

**To get request status information using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests**, and then select the Spot request\.

1. Check the value of **Status** in the **Description** tab\.

**To get request status information using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-spot\-instance\-requests](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-instance-requests.html) \(AWS CLI\)
+ [Get\-EC2SpotInstanceRequest](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2SpotInstanceRequest.html) \(AWS Tools for Windows PowerShell\)

## Spot Request Status Codes<a name="spot-instance-bid-status-understand"></a>

Spot request status information is composed of a status code, the update time, and a status message\. Together, these help you determine the disposition of your Spot request\.

The following are the Spot request status codes:

`az-group-constraint`  
Amazon EC2 cannot launch all the instances you requested in the same Availability Zone\.

`bad-parameters`  
One or more parameters for your Spot request are not valid \(for example, the AMI you specified does not exist\)\. The status message indicates which parameter is not valid\.

`cancelled-before-fulfillment`  
The user cancelled the Spot request before it was fulfilled\.

`capacity-not-available`  
There is not enough capacity available for the instances that you requested\.

`capacity-oversubscribed`  
There is not enough capacity available for the instances that you requested\.

`constraint-not-fulfillable`  
The Spot request can't be fulfilled because one or more constraints are not valid \(for example, the Availability Zone does not exist\)\. The status message indicates which constraint is not valid\.

`fulfilled`  
The Spot request is `active`, and Amazon EC2 is launching your Spot Instances\.

`instance-terminated-by-price`  
The Spot price exceeds your maximum price\. If your request is persistent, the process restarts, so your request is pending evaluation\.

`instance-terminated-by-service`  
Your instance was terminated from a stopped state\.

`instance-terminated-by-user` or `spot-instance-terminated-by-user`  
You terminated a Spot Instance that had been fulfilled, so the request state is `closed` \(unless it's a persistent request\) and the instance state is `terminated`\.

 `instance-terminated-capacity-oversubscribed`   
Your instance is terminated because the number of Spot requests with maximum prices equal to or higher than the Spot price exceeded the available capacity in this Spot Instance pool\. \(Note that the Spot price might not have changed\.\)

`instance-terminated-launch-group-constraint`  
One or more of the instances in your launch group was terminated, so the launch group constraint is no longer fulfilled\.

`instance-terminated-no-capacity`  
There is no longer enough Spot capacity available for the instance\.

`launch-group-constraint`  
Amazon EC2 cannot launch all the instances that you requested at the same time\. All instances in a launch group are started and terminated together\.

`limit-exceeded`  
The limit on the number of EBS volumes or total volume storage was exceeded\. For more information about these limits and how to request an increase, see [Amazon EBS Limits](http://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html#limits_ebs) in the *Amazon Web Services General Reference*\.

`marked-for-stop`  
The Spot Instance is marked for stopping\.

`marked-for-termination`  
The Spot Instance is marked for termination\.

`not-scheduled-yet`  
The Spot request will not be evaluated until the scheduled date\.

`pending-evaluation`  
After you make a Spot Instance request, it goes into the `pending-evaluation` state while the system evaluates the parameters of your request\.

`pending-fulfillment`  
Amazon EC2 is trying to provision your Spot Instances\.

`placement-group-constraint`  
The Spot request can't be fulfilled yet because a Spot Instance can't be added to the placement group at this time\.

`price-too-low`  
The request can't be fulfilled yet because your maximum price is below the Spot price\. In this case, no instance is launched and your request remains `open`\.

`request-canceled-and-instance-running`  
You canceled the Spot request while the Spot Instances are still running\. The request is `cancelled`, but the instances remain `running`\.

`schedule-expired`  
The Spot request expired because it was not fulfilled before the specified date\.

`system-error`  
There was an unexpected system error\. If this is a recurring issue, please contact customer support for assistance\.