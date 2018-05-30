# Managing an EC2 Fleet<a name="manage-ec2-fleet"></a>

To use an EC2 Fleet, you create a request that includes the target capacity, On\-Demand capacity, Spot capacity, one or more launch specifications for the instances, and the maximum price that you are willing to pay\. The fleet request must include a launch template that defines the information that the fleet needs to launch an instance, such as an AMI, instance type, subnet or Availability Zone, and one or more security groups\. You can specify launch specification overrides for the instance type, subnet, Availability Zone, and maximum price you're willing to pay, and you can assign weighted capacity to each launch specification override\.

If your fleet includes Spot Instances, Amazon EC2 attempts to maintain your fleet target capacity as Spot prices change\.

An EC2 Fleet request remains active until it expires or you delete it\. When you delete a fleet, you may specify whether deletion terminates the instances in that fleet\.

**Topics**
+ [EC2 Fleet Request States](#EC2-fleet-states)
+ [EC2 Fleet Prerequisites](#ec2-fleet-prerequisites)
+ [EC2 Fleet Health Checks](#ec2-fleet-health-checks)
+ [Generating an EC2 Fleet JSON Configuration File](#ec2-fleet-cli-skeleton)
+ [Creating an EC2 Fleet](#create-ec2-fleet)
+ [Monitoring Your EC2 Fleet](#manage-ec2-fleet)
+ [Modifying an EC2 Fleet](#modify-ec2-fleet)
+ [Deleting an EC2 Fleet](#delete-fleet)
+ [EC2 Fleet Example Configurations](ec2-fleet-examples.md)

## EC2 Fleet Request States<a name="EC2-fleet-states"></a>

An EC2 Fleet request can be in one of the following states:
+ `submitted` – The EC2 Fleet request is being evaluated and Amazon EC2 is preparing to launch the target number of instances, which can include On\-Demand Instances, Spot Instances, or both\.
+ `active` – The EC2 Fleet request has been validated and Amazon EC2 is attempting to maintain the target number of running instances\. The request remains in this state until it is modified or deleted\.
+ `modifying` – The EC2 Fleet request is being modified\. The request remains in this state until the modification is fully processed or the request is deleted\. A one\-time `request` cannot be modified, and this state does not apply to such requests\.
+ `deleted_running` – The EC2 Fleet request is deleted and does not launch additional instances\. Its existing instances continue to run until they are interrupted or terminated\. The request remains in this state until all instances are interrupted or terminated\.
+ `deleted_terminating` – The EC2 Fleet request is deleted and its instances are terminating\. The request remains in this state until all instances are terminated\.
+ `deleted` – The EC2 Fleet is deleted and has no running instances\. The request is deleted two days after its instances are terminated\.

The following illustration represents the transitions between the EC2 Fleet request states\. If you exceed your fleet limits, the request is deleted immediately\.

![\[EC2 Fleet request states\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EC2_fleet_states.png)

## EC2 Fleet Prerequisites<a name="ec2-fleet-prerequisites"></a>

To create an EC2 Fleet, the following prerequisites must be in place:
+ [Launch template](#ec2-fleet-prerequisites-launch-template)
+ [IAM role for EC2 Fleet](#ec2-fleet-prerequisites-role)
+ [Service\-linked role for EC2 Fleet](#ec2-fleet-service-linked-role)

### Launch Template<a name="ec2-fleet-prerequisites-launch-template"></a>

A launch template includes information about the instances to launch, such as the instance type, Availability Zone, and the maximum price that you are willing to pay\. For more information, see [Launching an Instance from a Launch Template](ec2-launch-templates.md)\.

### IAM Role for EC2 Fleet<a name="ec2-fleet-prerequisites-role"></a>

The `aws-ec2-fleet-tagging-role` grants the EC2 Fleet permission to request, launch, terminate, and tag instances on your behalf\. Ensure that this role exists before you use the AWS CLI or an API to create an EC2 Fleet\. To create the role, use the IAM console as follows\. 

**To create the IAM role for EC2 Fleet**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\.

1. On the **Select type of trusted entity** page, choose **AWS service**, **EC2**, **EC2 \- Fleet Tagging**, and then choose **Next: Permissions**\.

1. On the **Attached permissions policy** page, choose **Next:Review**\.

1. On the **Review** page, type a name for the role \(for example, **aws\-ec2\-fleet\-tagging\-role**\) and choose **Create role**\.

### EC2 Fleet and IAM Users<a name="ec2-fleet-iam-users"></a>

If your IAM users will create or manage an EC2 Fleet, be sure to grant them the required permissions as follows\.

**To grant an IAM user permissions for EC2 Fleet**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**, **Create policy**\.

1. On the **Create policy** page, choose the **JSON** tab, replace the text with the following, and choose **Review policy**\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "ec2:*"
               ],
               "Resource": "*"
           },
           {
               "Effect": "Allow",
               "Action": [
                 "iam:ListRoles",
                 "iam:PassRole",
                 "iam:ListInstanceProfiles"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

   The `ec2:*` grants an IAM user permission to call all Amazon EC2 API actions\. To limit the user to specific Amazon EC2 API actions, specify those actions instead\.

   An IAM user must have permission to call the `iam:ListRoles` action to enumerate existing IAM roles, the `iam:PassRole` action to specify the EC2 Fleet role, and the `iam:ListInstanceProfiles` action to enumerate existing instance profiles\.

   \(Optional\) To enable an IAM user to create roles or instance profiles using the IAM console, you must also add the following actions to the policy:
   + `iam:AddRoleToInstanceProfile`
   + `iam:AttachRolePolicy`
   + `iam:CreateInstanceProfile`
   + `iam:CreateRole`
   + `iam:GetRole`
   + `iam:ListPolicies`

1. On the **Review policy** page, type a policy name and description, and then choose **Create policy**\.

1. In the navigation pane, choose **Users** and select the user\.

1. On the **Permissions** tab, choose **Add permissions**\.

1. Choose **Attach existing policies directly**\. Select the policy that you created earlier and choose **Next: Review**\.

1. Choose **Add permissions**\.

### Service\-Linked Role for EC2 Fleet Requests<a name="ec2-fleet-service-linked-role"></a>

Before you create an EC2 Fleet, you need to create a service\-linked role named **AWSServiceRoleForEC2Fleet**\. A service\-linked role includes all the permissions that Amazon EC2 requires to call other AWS services on your behalf\. If you try to create a fleet before the role is created, you get an error\. Create the role using the IAM console\. For more information, see [Using Service\-Linked Roles](http://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.

Amazon EC2 uses the service\-linked role named **AWSServiceRoleForEC2Fleet** to complete the following actions:
+ `ec2:RequestSpotInstances` – Request Spot Instances\.
+ `ec2:TerminateInstances` – Terminate Spot Instances\.
+ `ec2:DescribeImages` – Describe Amazon Machine Images \(AMI\) for the Spot Instances\.
+ `ec2:DescribeInstanceStatus` – Describe the status of the Spot Instances\.
+ `ec2:DescribeSubnets` – Describe the subnets for Spot Instances\.
+ `ec2:CreateTags` \- Add system tags to Spot Instances\.

If you no longer need to use EC2 Fleet, we recommend that you delete the **AWSServiceRoleForEC2Fleet** role\. After this role is deleted from your account, you can create the role again if you create another fleet\.

## EC2 Fleet Health Checks<a name="ec2-fleet-health-checks"></a>

EC2 Fleet checks the health status of the instances in the fleet every two minutes\. The health status of an instance is either `healthy` or `unhealthy`\. The fleet determines the health status of an instance using the status checks provided by Amazon EC2\. If the status of either the instance status check or the system status check is `impaired` for three consecutive health checks, the health status of the instance is `unhealthy`\. Otherwise, the health status is `healthy`\. For more information, see [Status Checks for Your Instances](monitoring-system-instance-status-check.md)\.

You can configure your EC2 Fleet to replace unhealthy instances\. After enabling health check replacement, an instance is replaced after its health status is reported as `unhealthy`\. The fleet could go below its target capacity for up to a few minutes while an unhealthy instance is being replaced\.

**Requirements**
+ Health check replacement is supported only with EC2 Fleets that maintain a target capacity, not with one\-time fleets\.
+ You can configure your EC2 Fleet to replace unhealthy instances only when you create it\.
+ IAM users can use health check replacement only if they have permission to call the `ec2:DescribeInstanceStatus` action\.

## Generating an EC2 Fleet JSON Configuration File<a name="ec2-fleet-cli-skeleton"></a>

To create an EC2 Fleet, you need only specify the launch template, target capacity, and whether the default purchasing model is On\-Demand or Spot\. If you do not specify a parameter, the fleet uses the default value\. To view the full list of fleet configuration parameters, you can generate a JSON file as follows\.

**To generate a JSON file with all possible EC2 Fleet parameters using the command line**
+ Use the [create\-fleet](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) \(AWS CLI\) command and the `--generate-cli-skeleton` parameter to generate an EC2 Fleet JSON file:

  ```
  aws ec2 create-fleet --generate-cli-skeleton
  ```

  The following EC2 Fleet parameters are available:

  ```
      {
      "DryRun": true, 
      "ClientToken": "", 
      "SpotOptions": {
          "AllocationStrategy": "diversified", 
          "InstanceInterruptionBehavior": "stop"
      }, 
      "ExcessCapacityTerminationPolicy": "no-termination", 
      "LaunchTemplateConfigs": [
          {
              "LaunchTemplateSpecification": {
                  "LaunchTemplateId": "", 
                  "LaunchTemplateName": "", 
                  "Version": ""
              }, 
              "Overrides": [
                  {
                      "InstanceType": "t2.micro", 
                      "MaxPrice": "", 
                      "SubnetId": "", 
                      "AvailabilityZone": "", 
                      "WeightedCapacity": null
                  }
              ]
          }
      ], 
      "TargetCapacitySpecification": {
          "TotalTargetCapacity": 0, 
          "OnDemandTargetCapacity": 0, 
          "SpotTargetCapacity": 0, 
          "DefaultTargetCapacityType": "spot"
      }, 
      "TerminateInstancesWithExpiration": true, 
      "Type": "request", 
      "ValidFrom": "1970-01-01T00:00:00", 
      "ValidUntil": "1970-01-01T00:00:00", 
      "ReplaceUnhealthyInstances": true, 
      "TagSpecifications": [
          {
              "ResourceType": "reserved-instances", 
              "Tags": [
                  {
                      "Key": "", 
                      "Value": ""
                  }
              ]
          }
      ]
  }
  ```

### EC2 Fleet JSON Configuration File Reference<a name="ec2-fleet-json-reference"></a>

**Note**  
Use lowercase for all parameter values; otherwise, you get an error when Amazon EC2 uses the JSON file to launch the EC2 Fleet\.

**AllocationStrategy**  
\(Optional\) Indicates how to allocate the Spot Instance target capacity across the Spot Instance pools specified by the EC2 Fleet\. Valid values are `lowest-price` and `diversified`\. The default is `lowest-price`\. Specify the allocation strategy that meets your needs\. For more information, see [Allocation Strategy for Spot Instances](ec2-fleet-configuration-strategies.md#ec2-fleet-allocation-strategy)\.

**InstanceInterruptionBehavior**  
\(Optional\) The behavior when a Spot Instance is interrupted\. Valid values are `hibernate`, `stop`, and `terminate`\. By default, the Spot service terminates Spot Instances when they are interrupted\. If the fleet type is `maintain`, you can specify that the Spot service hibernates or stops Spot Instances when they are interrupted\.

**ExcessCapacityTerminationPolicy**  
\(Optional\) Indicates whether running instances should be terminated if the total target capacity of the EC2 Fleet is decreased below the current size of the EC2 Fleet\. Valid values are `no-termination` and `termination`\.

**LaunchTemplateId**  
The ID of the launch template to use\. You must specify either the launch template ID or launch template name\. The launch template must specify an Amazon Machine Image \(AMI\)\. For more information about creating launch templates, see [Launching an Instance from a Launch Template](ec2-launch-templates.md)\.

**LaunchTemplateName**  
The name of the launch template to use\. You must specify either the launch template ID or launch template name\. The launch template must specify an Amazon Machine Image \(AMI\)\. For more information, see [Launching an Instance from a Launch Template](ec2-launch-templates.md)\.

**Version**  
The version number of the launch template\.

**InstanceType**  
\(Optional\) The instance type\. If entered, this value overrides the launch template\. The instance types must have the minimum hardware specifications that you need \(vCPUs, memory, or storage\)\.

**MaxPrice**  
\(Optional\) The maximum price per unit hour that you are willing to pay for a Spot Instance\. If entered, this value overrides the launch template\. You can use the default maximum price \(the On\-Demand price\) or specify the maximum price that you are willing to pay\. Your Spot Instances are not launched if your maximum price is lower than the Spot price for the instance types that you specified\.

**SubnetId**  
\(Optional\) The ID of the subnet in which to launch the instances\. If entered, this value overrides the launch template\. Your account supports either the EC2\-Classic and EC2\-VPC platforms, or the EC2\-VPC platform only\. To find out which platforms your account supports, see [Supported Platforms](ec2-supported-platforms.md)\.  
\[New VPC\] To create a new VPC, go the Amazon VPC console\. When you are done, return to the JSON file and enter the new subnet ID\.

**AvailabilityZone**  
\(Optional\) The Availability Zone in which to launch the instances\. The default is to let AWS choose the zones for your instances\. If you prefer, you can specify specific zones\. If entered, this value overrides the launch template\.   
\[EC2\-VPC\] Specify one or more Availability Zones\. If you have more than one subnet in a zone, specify the appropriate subnet\. To add subnets, go to the Amazon VPC console\. When you are done, return to the JSON file and enter the new subnet ID\.

**WeightedCapacity**  
\(Optional\) The number of units provided by the specified instance type\. If entered, this value overrides the launch template\.

**TotalTargetCapacity**  
The number of instances to launch\. You can choose instances or performance characteristics that are important to your application workload, such as vCPUs, memory, or storage\. If the request type is `maintain`, you can specify a target capacity of 0 and add capacity later\.

**OnDemandTargetCapacity**  
\(Optional\) The number of On\-Demand Instances to launch\. This number must be less than the `TotalTargetCapacity`\.

**SpotTargetCapacity**  
\(Optional\) The number of Spot Instances to launch\. This number must be less than the `TotalTargetCapacity`\.

**DefaultTargetCapacityType**  
If the value for `TotalTargetCapacity` is higher than the combined values for `OnDemandTargetCapacity` and `SpotTargetCapacity`, the difference is launched as the instance purchasing model specified here\. Valid values are `on-demand` or `spot`\.

**TerminateInstancesWithExpiration**  
\(Optional\) By default, Amazon EC2 terminates your instances when the EC2 Fleet request expires\. The default value is `true`\. To keep them running after your request expires, do not enter a value for this parameter\.

**Type**  
\(Optional\) Indicates whether EC2 Fleet only requests the target capacity, or also attempts to maintain it\. Valid values are `maintain` and `request`\. The default value is `maintain`\. If the value is `request`, EC2 Fleet only places the required requests\. It does not attempt to replenish instances if capacity is diminished, and does not submit requests in alternative capacity pools if capacity is unavailable\. If the value is `maintain`, the fleet places the required requests to meet the target capacity\. It also automatically replenishes any interrupted Spot Instances\. 

**ValidFrom**  
\(Optional\) To create a request that is valid only during a specific time period, enter a start date\.

**ValidUntil**  
\(Optional\) To create a request that is valid only during a specific time period, enter an end date\.

**ReplaceUnhealthyInstances**  
\(Optional\) To replace unhealthy instances in an EC2 Fleet that is configured to `maintain` the fleet, enter `true`\. Otherwise, leave this parameter empty\.

**TagSpecifications**  
\(Optional\) The key\-value pair for tagging Amazon EC2 resources\.

## Creating an EC2 Fleet<a name="create-ec2-fleet"></a>

When you create an EC2 Fleet, you must specify a launch template that includes information about the instances to launch, such as the instance type, Availability Zone, and the maximum price you are willing to pay\.

You can create an EC2 Fleet that includes multiple launch specifications that override the launch template\. The launch specifications can vary by instance type, Availability Zone, subnet, and maximum price, and can include a different weighted capacity\.

When you create an EC2 Fleet, use a JSON file to specify information about the instances to launch\. For more information, see [EC2 Fleet JSON Configuration File Reference](#ec2-fleet-json-reference)\. 

**To create an EC2 Fleet using the AWS CLI**
+ Use the following [create\-fleet](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) \(AWS CLI\) command to create an EC2 Fleet:

```
aws ec2 create-fleet --cli-input-json file://file_name.json
```

For example configuration files, see [EC2 Fleet Example Configurations](ec2-fleet-examples.md)\.

The following is example output:

```
{
    "FleetId": "fleet-12a34b55-67cd-8ef9-ba9b-9208dEXAMPLE"
}
```

## Monitoring Your EC2 Fleet<a name="manage-ec2-fleet"></a>

The EC2 Fleet launches On\-Demand Instances when there is available capacity, and launches Spot Instances when your maximum price exceeds the Spot price and capacity is available\. The On\-Demand Instances run until you terminate them, and the Spot Instances run until they are interrupted or you terminate them\.

The returned list of running instances is refreshed periodically and might be out of date\.

**To monitor your EC2 Fleet using the AWS CLI**  
Use the following [describe\-fleets](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-fleet.html) command to describe your EC2 Fleets:

```
aws ec2 describe-fleets
```

The following is example output:

```
{
    "Fleets": [
        {
            "Type": "maintain", 
            "FulfilledCapacity": 2.0, 
            "LaunchTemplateConfigs": [
                {
                    "LaunchTemplateSpecification": {
                        "Version": "2", 
                        "LaunchTemplateId": "lt-07b3bc7625cdab851"
                    }
                }
            ], 
            "TerminateInstancesWithExpiration": false, 
            "TargetCapacitySpecification": {
                "OnDemandTargetCapacity": 0, 
                "SpotTargetCapacity": 2, 
                "TotalTargetCapacity": 2, 
                "DefaultTargetCapacityType": "spot"
            }, 
            "FulfilledOnDemandCapacity": 0.0, 
            "ActivityStatus": "fulfilled", 
            "FleetId": "fleet-76e13e99-01ef-4bd6-ba9b-9208de883e7f", 
            "ReplaceUnhealthyInstances": false, 
            "SpotOptions": {
                "InstanceInterruptionBehavior": "terminate", 
                "AllocationStrategy": "lowestPrice"
            }, 
            "FleetState": "active", 
            "ExcessCapacityTerminationPolicy": "termination", 
            "CreateTime": "2018-04-10T16:46:03.000Z"
        }
    ]
}
```

Use the following [describe\-fleet\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-fleet-instances.html) command to describe the instances for the specified EC2 Fleet:

```
aws ec2 describe-fleet-instances --fleet-id fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE
```

```
{
    "ActiveInstances": [
        {
            "InstanceId": "i-09cd595998cb3765e", 
            "InstanceHealth": "healthy", 
            "InstanceType": "m4.large", 
            "SpotInstanceRequestId": "sir-86k84j6p"
        }, 
        {
            "InstanceId": "i-09cf95167ca219f17", 
            "InstanceHealth": "healthy", 
            "InstanceType": "m4.large", 
            "SpotInstanceRequestId": "sir-dvxi7fsm"
        }
    ], 
    "FleetId": "fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE"
}
```

Use the following [describe\-fleet\-history](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-fleet-request-history.html) command to describe the history for the specified EC2 Fleet for the specified time: 

```
aws ec2 describe-fleet-history --fleet-request-id fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE --start-time 2018-04-10T00:00:00Z
```

```
{
    "HistoryRecords": [], 
    "FleetId": "fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE", 
    "LastEvaluatedTime": "1970-01-01T00:00:00.000Z", 
    "StartTime": "2018-04-09T23:53:20.000Z"
}
```

## Modifying an EC2 Fleet<a name="modify-ec2-fleet"></a>

You can modify an EC2 Fleet that is in the `submitted` or `active` state\. When you modify a fleet, it enters the `modifying` state\.

You can modify the following parameters of an EC2 Fleet:
+ `target-capacity` – Increase or decrease the target capacity\.
+ `excess-capacity-termination-policy` – Whether running instances should be terminated if the total target capacity of the EC2 Fleet is decreased below the current size of the fleet\. Valid values are `no-termination` and `termination`\.

**Note**  
You can only modify an EC2 Fleet that has `Type`=`maintain`\.

When you increase the target capacity, the EC2 Fleet launches the additional instances according to the instance purchasing model specified for `DefaultTargetCapacityType`, which are either On\-Demand Instances or Spot Instances\.

If the `DefaultTargetCapacityType` is `spot`, the EC2 Fleet launches the additional Spot Instances according to its allocation strategy\. If the allocation strategy is `lowestPrice`, the fleet launches the instances from the lowest\-priced Spot Instance pool in the request\. If the allocation strategy is `diversified`, the fleet distributes the instances across the pools in the request\.

When you decrease the target capacity, the EC2 Fleet deletes any open requests that exceed the new target capacity\. You can request that the fleet terminate instances until the size of the fleet reaches the new target capacity\. If the allocation strategy is `lowestPrice`, the fleet terminates the instances with the highest price per unit\. If the allocation strategy is `diversified`, the fleet terminates instances across the pools\. Alternatively, you can request that EC2 Fleet keep the fleet at its current size, but not replace any Spot Instances that are interrupted or any instances that you terminate manually\.

When an EC2 Fleet terminates a Spot Instance because the target capacity was decreased, the instance receives a Spot Instance interruption notice\.

**To modify an EC2 Fleet using the AWS CLI**  
Use the following [modify\-fleet](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-spot-fleet-request.html) command to update the target capacity of the specified EC2 Fleet:

```
aws ec2 modify-fleet --fleet-id fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE --target-capacity 20
```

If you are decreasing the target capacity but want to keep the fleet at its current size, you can modify the previous command as follows:

```
aws ec2 modify-fleet --fleet-id fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE --target-capacity 10 --excess-capacity-termination-policy no-termination
```

## Deleting an EC2 Fleet<a name="delete-fleet"></a>

If you no longer require an EC2 Fleet, you can delete it\. After you delete a fleet, it launches no new instances\.

You must specify whether the EC2 Fleet must terminate its instances\. If you specify that the instances must be terminated when the fleet is deleted, it enters the `deleted_terminating` state\. Otherwise, it enters the `deleted_running` state, and the instances continue to run until they are interrupted or you terminate them manually\.

**To delete an EC2 Fleet using the AWS CLI**  
Use the [delete\-fleets](http://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-spot-fleet-requests.html) command and the `--terminate-instances` parameter to delete the specified EC2 Fleet and terminate the instances:

```
aws ec2 delete-fleets --fleet-ids fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE --terminate-instances
```

The following is example output:

```
{
    "UnsuccessfulFleetDeletions": [], 
    "SuccessfulFleetDeletions": [
        {
            "CurrentFleetState": "deleted_terminating", 
            "PreviousFleetState": "active", 
            "FleetId": "fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE"
        }
    ]
}
```

You can modify the previous command using the `--no-terminate-instances` parameter to delete the specified EC2 Fleet without terminating the instances:

```
aws ec2 delete-fleets --fleet-ids fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE --no-terminate-instances
```

The following is example output:

```
{
    "UnsuccessfulFleetDeletions": [], 
    "SuccessfulFleetDeletions": [
        {
            "CurrentFleetState": "deleted_running", 
            "PreviousFleetState": "active", 
            "FleetId": "fleet-4b8aaae8-dfb5-436d-a4c6-3dafa4c6b7dcEXAMPLE"
        }
    ]
}
```