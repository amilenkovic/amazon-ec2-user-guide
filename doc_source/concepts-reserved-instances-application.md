# How You Are Billed<a name="concepts-reserved-instances-application"></a>

All Reserved Instances provide you with a discount compared to On\-Demand pricing\. With Reserved Instances, you pay for the entire term regardless of actual use\. You can choose to pay for your Reserved Instance upfront, partially upfront, or monthly, depending on the [payment option](ec2-reserved-instances.md#ri-payment-options) specified for the Reserved Instance\. 

When Reserved Instances expire, you are charged On\-Demand rates for EC2 instance usage\. You can set up a billing alert to warn you when your bill exceeds a threshold you define\. For more information, see [Monitoring Charges with Alerts and Notifications](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/monitor-charges.html) in the *AWS Billing and Cost Management User Guide*\.

**Note**  
The AWS Free Tier is available for new AWS accounts\. If you are using the AWS Free Tier to run Amazon EC2 instances, and you purchase a Reserved Instance, you are charged under standard pricing guidelines\. For information, see [AWS Free Tier](https://aws.amazon.com//free)\.

**Topics**
+ [Usage Billing](#hourly-billing)
+ [Viewing Your Bill](#ri-market-buyer-billing)
+ [Reserved Instances and Consolidated Billing](#concepts-reserved-instances-billing)
+ [Reserved Instance Discount Pricing Tiers](#reserved-instances-discounts)

## Usage Billing<a name="hourly-billing"></a>

Reserved Instances are billed for every clock\-hour during the term that you select, regardless of whether an instance is running or not\. A clock\-hour is defined as the standard 24\-hour clock that runs from midnight to midnight, and is divided into 24 hours \(for example, 1:00:00 to 1:59:59 is one clock\-hour\)\. For more information about instance states, see [Instance Lifecycle](ec2-instance-lifecycle.md)\.

 





A Reserved Instance billing benefit is applied to a running instance on a per\-second basis\. A Reserved Instance billing benefit can apply to a maximum of 3600 seconds \(one hour\) of instance usage per clock\-hour\. You can run multiple instances concurrently, but can only receive the benefit of the Reserved Instance discount for a total of 3600 seconds per clock\-hour; instance usage that exceeds 3600 seconds in a clock\-hour is billed at the On\-Demand rate\.

For example, if you purchase one `m4.xlarge` Reserved Instance and run four `m4.xlarge` instances concurrently for one hour, one instance is charged at one hour of Reserved Instance usage and the other three instances are charged at three hours of On\-Demand usage\.

However, if you purchase one `m4.xlarge` Reserved Instance and run four `m4.xlarge` instances for 15 minutes \(900 seconds\) each within the same hour, the total running time for the instances is one hour, which results in one hour of Reserved Instance usage and 0 hours of On\-Demand usage\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ri-per-second-billing.png)

If multiple eligible instances are running concurrently, the Reserved Instance billing benefit is applied to all the instances at the same time up to a maximum of 3600 seconds in a clock\-hour; thereafter, On\-Demand rates apply\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ri-per-second-billing-concurrent.png)

**Cost Explorer** on the [Billing and Cost Management](https://console.aws.amazon.com/billing) console enables you to analyze the savings against running On\-Demand Instances\. The [Reserved Instances FAQ](https://aws.amazon.com/ec2/faqs/#reserved-instances) includes an example of a list value calculation\.

If you close your AWS account, On\-Demand billing for your resources stops\. However, if you have any Reserved Instances in your account, you continue to receive a bill for these until they expire\.

## Viewing Your Bill<a name="ri-market-buyer-billing"></a>

You can find out about the charges and fees to your account by viewing the [AWS Billing and Cost Management](https://console.aws.amazon.com//billing) console\.
+ The **Dashboard** displays a spend summary for your account\.
+ On the **Bills** page, under **Details** expand the **Elastic Compute Cloud** section and the region to get billing information about your Reserved Instances\.

You can view the charges online, or you can download a CSV file\.

You can also track your Reserved Instance utilization using the AWS Cost and Usage Report\. For more information, see [Reserved Instances ](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage-ri.html) under Cost and Usage Report in the *AWS Billing and Cost Management User Guide*\.

## Reserved Instances and Consolidated Billing<a name="concepts-reserved-instances-billing"></a>

The pricing benefits of Reserved Instances are shared when the purchasing account is part of a set of accounts billed under one consolidated billing payer account\. The instance usage across all member accounts is aggregated in the payer account every month\. This is typically useful for companies in which there are different functional teams or groups; then, the normal Reserved Instance logic is applied to calculate the bill\. For more information, see [Consolidated Billing and AWS Organizations](http://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_from-consolidatedbilling.html) in the *AWS Organizations User Guide*\.

If you close the payer account, any member accounts that benefit from Reserved Instances billing discounts continue to benefit from the discount until the Reserved Instances expire, or until the member account is removed\.

## Reserved Instance Discount Pricing Tiers<a name="reserved-instances-discounts"></a>

If your account qualifies for a discount pricing tier, it automatically receives discounts on upfront and instance usage fees for Reserved Instance purchases that you make within that tier level from that point on\. To qualify for a discount, the list value of your Reserved Instances in the region must be $500,000 USD or more\.

The following rules apply:
+ Pricing tiers and related discounts apply only to purchases of Amazon EC2 Standard Reserved Instances\.
+ Pricing tiers do not apply to Reserved Instances for Windows with SQL Server Standard or Windows with SQL Server Web\.
+ Pricing tier discounts only apply to purchases made from AWS\. They do not apply to purchases of third\-party Reserved Instances\. 
+ Discount pricing tiers are currently not applicable to Convertible Reserved Instance purchases\. 

**Topics**
+ [Calculating Reserved Instance Pricing Discounts](#pricing-discounts)
+ [Buying with a Discount Tier](#buying-discount-tier)
+ [Crossing Pricing Tiers](#crossing-pricing-tiers)
+ [Consolidated Billing for Pricing Tiers](#consolidating-billing)

### Calculating Reserved Instance Pricing Discounts<a name="pricing-discounts"></a>

You can determine the pricing tier for your account by calculating the list value for all of your Reserved Instances in a region\. Multiply the hourly recurring price for each reservation by the total number of hours for the term and add the undiscounted upfront price \(also known as the fixed price\) listed on the [Reserved Instances pricing page](https://aws.amazon.com/ec2/pricing/reserved-instances/pricing/) at the time of purchase\. Because the list value is based on undiscounted \(public\) pricing, it is not affected if you qualify for a volume discount or if the price drops after you buy your Reserved Instances\.

```
List value = fixed price + (undiscounted recurring hourly price * hours in term)
```

For example, for a 1\-year Partial Upfront `t2.small` Reserved Instance, assume the upfront price is $60\.00 and the hourly rate is $0\.007\. This provides a list value of $121\.32\.

```
121.32 = 60.00 + (0.007 * 8760)
```

**To view the fixed price values for Reserved Instances using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Reserved Instances**\.

1. Display the **Upfront Price** column by choosing **Show/Hide Columns** \(the gear\-shaped icon\) in the top right corner\.

**To view the fixed price values for Reserved Instances using the command line**
+ [describe\-reserved\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances.html) \(AWS CLI\)
+  [Get\-EC2ReservedInstance](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstance.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeReservedInstances](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeReservedInstances.html) \(Amazon EC2 API\)

### Buying with a Discount Tier<a name="buying-discount-tier"></a>

When you buy Reserved Instances, Amazon EC2 automatically applies any discounts to the part of your purchase that falls within a discount pricing tier\. You don't need to do anything differently, and you can buy Reserved Instances using any of the Amazon EC2 tools\. For more information, see [Buying Reserved Instances](ri-market-concepts-buying.md)\.

After the list value of your active Reserved Instances in a region crosses into a discount pricing tier, any future purchase of Reserved Instances in that region are charged at a discounted rate\. If a single purchase of Reserved Instances in a region takes you over the threshold of a discount tier, then the portion of the purchase that is above the price threshold is charged at the discounted rate\. For more information about the temporary Reserved Instance IDs that are created during the purchase process, see [Crossing Pricing Tiers](#crossing-pricing-tiers)\.

If your list value falls below the price point for that discount pricing tier—for example, if some of your Reserved Instances expire—future purchases of Reserved Instances in the region are not discounted\. However, you continue to get the discount applied against any Reserved Instances that were originally purchased within the discount pricing tier\.

When you buy Reserved Instances, one of four possible scenarios occurs:
+ **No discount**—Your purchase within a region is still below the discount threshold\.
+ **Partial discount**—Your purchase within a region crosses the threshold of the first discount tier\. No discount is applied to one or more reservations and the discounted rate is applied to the remaining reservations\.
+ **Full discount**—Your entire purchase within a region falls within one discount tier and is discounted appropriately\.
+ **Two discount rates**—Your purchase within a region crosses from a lower discount tier to a higher discount tier\. You are charged two different rates: one or more reservations at the lower discounted rate, and the remaining reservations at the higher discounted rate\.

### Crossing Pricing Tiers<a name="crossing-pricing-tiers"></a>

If your purchase crosses into a discounted pricing tier, you see multiple entries for that purchase: one for that part of the purchase charged at the regular price, and another for that part of the purchase charged at the applicable discounted rate\.

The Reserved Instance service generates several Reserved Instance IDs because your purchase crossed from an undiscounted tier, or from one discounted tier to another\. There is an ID for each set of reservations in a tier\. Consequently, the ID returned by your purchase CLI command or API action is different from the actual ID of the new Reserved Instances\.

### Consolidated Billing for Pricing Tiers<a name="consolidating-billing"></a>

A consolidated billing account aggregates the list value of member accounts within a region\. When the list value of all active Reserved Instances for the consolidated billing account reaches a discount pricing tier, any Reserved Instances purchased after this point by any member of the consolidated billing account are charged at the discounted rate \(as long as the list value for that consolidated account stays above the discount pricing tier threshold\)\. For more information, see [Reserved Instances and Consolidated Billing](#concepts-reserved-instances-billing)\. 