# Types of Reserved Instances \(Offering Classes\)<a name="reserved-instances-types"></a>

When you purchase a Reserved Instance, you can choose between a Standard or Convertible offering class\. The Reserved Instance applies to a single instance family, platform, scope, and tenancy over a term\. If your computing needs change, you may be able to modify or exchange your Reserved Instance, depending on the offering class\. Offering classes may also have additional restrictions or limitations\.

The following are the differences between Standard and Convertible offering classes\.


****  

| Standard Reserved Instance | Convertible Reserved Instance | 
| --- | --- | 
| Some attributes, such as instance size, can be modified during the term; however, the instance type cannot be modified\. You cannot exchange a Standard Reserved Instance, only modify it\. For more information, see [Modifying Reserved Instances](ri-modifying.md)\. | Can be exchanged during the term for another Convertible Reserved Instance with new attributes including instance family, instance type, platform, scope, or tenancy\. For more information, see [Exchanging Convertible Reserved Instances](ri-convertible-exchange.md)\. You can also modify some attributes of a Convertible Reserved Instance\. For more information, see [Modifying Reserved Instances](ri-modifying.md)\. | 
| Can be sold in the Reserved Instance Marketplace\. | Cannot be sold in the Reserved Instance Marketplace\. | 

Standard and Convertible Reserved Instances can be purchased to apply to instances in a specific Availability Zone, or to instances in a region\. When you purchase a Reserved Instance in a specific Availability Zone, it provides a capacity reservation\. When you purchase a Reserved Instance for a region, it's referred to as a *regional Reserved Instance*\. Regional Reserved Instances do not provide a capacity reservation\. 

Regional Reserved Instances have the following attributes:
+ **Availability Zone flexibility**: the Reserved Instance discount applies to instance usage in any Availability Zone in a region\.
+ **Instance size flexibility**: the Reserved Instance discount applies to instance usage regardless of size, within that instance family\. Only supported on Linux/Unix Reserved Instances with default tenancy\.

For more information and examples, see [How Reserved Instances Are Applied](apply_ri.md)\. 

If you want to purchase capacity reservations that recur on a daily, weekly, or monthly basis, a Scheduled Reserved Instance may meet your needs\. For more information, see [Scheduled Reserved Instances](ec2-scheduled-instances.md)\.