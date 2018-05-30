# Reserved Instances<a name="ec2-reserved-instances"></a>

Reserved Instances provide you with a significant discount compared to On\-Demand Instance pricing\. Reserved Instances are not physical instances, but rather a billing discount applied to the use of On\-Demand Instances in your account\. These On\-Demand Instances must match certain attributes in order to benefit from the billing discount\.

The following diagram shows a basic overview of purchasing and using Reserved Instances\. 

![\[Purchasing Reserved Instances\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ri-basics.png)

In this scenario, you have a running On\-Demand Instance \(T2\) in your account, for which you're currently paying On\-Demand rates\. You purchase a Reserved Instance that matches the attributes of your running instance, and the billing benefit is immediately applied\. Next, you purchase a Reserved Instance for a C4 instance\. You do not have any running instances in your account that match the attributes of this Reserved Instance\. In the final step, you launch an instance that matches the attributes of the C4 Reserved Instance, and the billing benefit is immediately applied\.

When you purchase a Reserved Instance, choose a combination of the following that suits your needs:
+ **Payment option**: No Upfront, Partial Upfront, or All Upfront\.
+ **Term**: One\-year or three\-year\. A year is defined as 31536000 seconds \(365 days\)\. Three years is defined as 94608000 seconds \(1095 days\)\.
+ **Offering class**: Convertible or Standard\.

In addition, a Reserved Instance has a number of attributes that determine how it is applied to a running instance in your account:
+ **Instance type**: For example, `m4.large`\. This is composed of the instance family \(m4\) and the instance size \(large\)\.
+ **Scope**: Whether the Reserved Instance applies to a region or specific Availability Zone\.
+ **Tenancy**: Whether your instance runs on shared \(default\) or single\-tenant \(dedicated\) hardware\. For more information, see [Dedicated Instances](dedicated-instance.md)\. 
+ **Platform**: The operating system; for example, Windows or Linux/Unix\. For more information, see [Choosing a Platform](ri-market-concepts-buying.md#ri-choosing-platform)\.

Reserved Instances do not renew automatically; when they expire, you can continue using the EC2 instance without interruption, but you are charged On\-Demand rates\. In the above example, when the Reserved Instances that cover the T2 and C4 instances expire, you go back to paying the On\-Demand rates until you terminate the instances or purchase new Reserved Instances that match the instance attributes\.

After you purchase a Reserved Instance, you cannot cancel your purchase\. However, you may be able to [modify](ri-modifying.md), [exchange](ri-convertible-exchange.md), or [sell](ri-market-general.md) your Reserved Instance if your needs change\.

## Payment Options<a name="ri-payment-options"></a>

The following payment options are available for Reserved Instances\.
+ **No Upfront** – You are billed a discounted hourly rate for every hour within the term, regardless of whether the Reserved Instance is being used\. No upfront payment is required\.
**Note**  
No Upfront Reserved Instances are based on a contractual obligation to pay monthly for the entire term of the reservation\. For this reason, a successful billing history is required before you can purchase No Upfront Reserved Instances\.
+ **Partial Upfront** – A portion of the cost must be paid upfront and the remaining hours in the term are billed at a discounted hourly rate, regardless of whether the Reserved Instance is being used\.
+ **All Upfront** – Full payment is made at the start of the term, with no other costs or additional hourly charges incurred for the remainder of the term, regardless of hours used\.

Generally speaking, you can save more money choosing Reserved Instances with a higher upfront payment\. You can also find Reserved Instances offered by third\-party sellers at lower prices and shorter term lengths on the Reserved Instance Marketplace\. For more information, see [Selling on the Reserved Instance Marketplace](ri-market-general.md)\. 

For more information about pricing, see [Amazon EC2 Reserved Instances Pricing](https://aws.amazon.com//ec2/pricing/reserved-instances/pricing/)\.

## Using Reserved Instances in a VPC<a name="reserved-instances-vpc"></a>

If your account supports EC2\-Classic, you can purchase Reserved Instances for use in either EC2\-Classic or a VPC\. You can purchase Reserved Instances to apply to instances launched into a VPC by selecting a platform that includes *Amazon VPC* in its name\. 

If you have an EC2\-VPC\-only account, the listed platforms available do not include *Amazon VPC* in its name because all instances must be launched into a VPC\.

For more information, see [Detecting Your Supported Platforms and Whether You Have a Default VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/default-vpc.html#detecting-platform)\. 

## Reserved Instance Limits<a name="ri-limits"></a>

You are limited to purchasing 20 Reserved Instances per Availability Zone, per month, plus 20 regional Reserved Instances\. Therefore, in a region that has three Availability Zones, you can purchase 80 Reserved Instances in total: 20 per Availability Zone \(60\) plus 20 regional Reserved Instances\.

Reserved Instances that are purchased for a specific Availability Zone \(zonal Reserved Instances\) allow you to launch as many instances that are covered by the zonal Reserved Instances, even if this results in your exceeding your On\-Demand Instance limit\. For example, your running On\-Demand Instance limit is 20, and you are currently running 18 On\-Demand Instances\. You have five unused zonal Reserved Instances\. You can launch two more On\-Demand Instances with any specifications, and you can launch five instances that exactly match the specifications of your zonal Reserved Instances; giving you a total of 25 instances\.

Regional Reserved Instances do not increase your On\-Demand Instance limit\.