# Amazon EC2 Instance Store<a name="InstanceStorage"></a>

An *instance store* provides temporary block\-level storage for your instance\. This storage is located on disks that are physically attached to the host computer\. Instance store is ideal for temporary storage of information that changes frequently, such as buffers, caches, scratch data, and other temporary content, or for data that is replicated across a fleet of instances, such as a load\-balanced pool of web servers\.

An instance store consists of one or more instance store volumes exposed as block devices\. The size of an instance store as well as the number of devices available varies by instance type\. While an instance store is dedicated to a particular instance, the disk subsystem is shared among instances on a host computer\.

The virtual devices for instance store volumes are `ephemeral[0-23]`\. Instance types that support one instance store volume have `ephemeral0`\. Instance types that support two instance store volumes have `ephemeral0` and `ephemeral1`, and so on\.

![\[Amazon EC2 instance storage\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/instance_storage.png)

**Topics**
+ [Instance Store Lifetime](#instance-store-lifetime)
+ [Instance Store Volumes](#instance-store-volumes)
+ [Add Instance Store Volumes to Your EC2 Instance](add-instance-store-volumes.md)
+ [SSD Instance Store Volumes](ssd-instance-store.md)
+ [Instance Store Swap Volumes](instance-store-swap-volumes.md)
+ [Optimizing Disk Performance for Instance Store Volumes](disk-performance.md)

## Instance Store Lifetime<a name="instance-store-lifetime"></a>

You can specify instance store volumes for an instance only when you launch it\. You can't detach an instance store volume from one instance and attach it to a different instance\.

The data in an instance store persists only during the lifetime of its associated instance\. If an instance reboots \(intentionally or unintentionally\), data in the instance store persists\. However, data in the instance store is lost under the following circumstances:
+ The underlying disk drive fails
+ The instance stops
+ The instance terminates

Therefore, do not rely on instance store for valuable, long\-term data\. Instead, use more durable data storage, such as Amazon S3, Amazon EBS, or Amazon EFS\.

When you stop or terminate an instance, every block of storage in the instance store is reset\. Therefore, your data cannot be accessed through the instance store of another instance\.

If you create an AMI from an instance, the data on its instance store volumes isn't preserved and isn't present on the instance store volumes of the instances that you launch from the AMI\.

## Instance Store Volumes<a name="instance-store-volumes"></a>

The instance type determines the size of the instance store available and the type of hardware used for the instance store volumes\. Instance store volumes are included as part of the instance's usage cost\. You must specify the instance store volumes that you'd like to use when you launch the instance \(except for NVMe instance store volumes, which are available by default\)\. Then format and mount the instance store volumes before using them\. You can't make an instance store volume available after you launch the instance\. For more information, see [Add Instance Store Volumes to Your EC2 Instance](add-instance-store-volumes.md)\.

Some instance types use NVMe or SATA\-based solid state drives \(SSD\) to deliver high random I/O performance\. This is a good option when you need storage with very low latency, but you don't need the data to persist when the instance terminates or you can take advantage of fault\-tolerant architectures\. For more information, see [SSD Instance Store Volumes](ssd-instance-store.md)\.

The following table provides the quantity, size, type, and performance optimizations of instance store volumes available on each supported instance type\. For a complete list of instance types, including EBS\-only types, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.


| Instance Type | Instance Store Volumes | Type | Needs Initialization\* | TRIM Support\*\* | 
| --- | --- | --- | --- | --- | 
|  `c1.medium`  |  1 x 350 GB†  | HDD | ✔ |  | 
|  `c1.xlarge`  |  4 x 420 GB \(1,680 GB\)  | HDD | ✔ |  | 
|  `c3.large`  |  2 x 16 GB \(32 GB\)  | SSD | ✔ |  | 
|  `c3.xlarge`  |  2 x 40 GB \(80 GB\)  | SSD | ✔ |  | 
|  `c3.2xlarge`  |  2 x 80 GB \(160 GB\)  | SSD | ✔ |  | 
|  `c3.4xlarge`  |  2 x 160 GB \(320 GB\)  | SSD | ✔ |  | 
|  `c3.8xlarge`  |  2 x 320 GB \(640 GB\)  | SSD | ✔ |  | 
|  `cc2.8xlarge`  |  4 x 840 GB \(3,360 GB\)  | HDD | ✔ |  | 
|  `cr1.8xlarge`  |  2 x 120 GB \(240 GB\)  | SSD | ✔ |  | 
|  `d2.xlarge`  |  3 x 2,000 GB \(6 TB\)  | HDD |  |  | 
|  `d2.2xlarge`  |  6 x 2,000 GB \(12 TB\)  | HDD |  |  | 
|  `d2.4xlarge`  |  12 x 2,000 GB \(24 TB\)  | HDD |  |  | 
|  `d2.8xlarge`  |  24 x 2,000 GB \(48 TB\)  | HDD |  |  | 
|  `f1.2xlarge`  |  1 x 470 GB  | NVMe SSD |  | ✔ | 
|  `f1.16xlarge`  |  4 x 940 GB  | NVMe SSD |  | ✔ | 
| g2\.2xlarge | 1 x 60 GB | SSD | ✔ |  | 
| g2\.8xlarge | 2 x 120 GB \(240 GB\) | SSD | ✔ |  | 
| h1\.2xlarge | 1 x 2000 GB \(2 TB\) | HDD |  |  | 
| h1\.4xlarge | 2 x 2000 GB \(4 TB\) | HDD |  |  | 
| h1\.8xlarge | 4 x 2000 GB \(8 TB\) | HDD |  |  | 
| h1\.16xlarge | 8 x 2000 GB \(16 TB\) | HDD |  |  | 
|  `hs1.8xlarge`  |  24 x 2,000 GB \(48 TB\)  | HDD | ✔ |  | 
|  `i2.xlarge`  |  1 x 800 GB  | SSD |  | ✔ | 
|  `i2.2xlarge`  |  2 x 800 GB \(1,600 GB\)  | SSD |  | ✔ | 
|  `i2.4xlarge`  |  4 x 800 GB \(3,200 GB\)  | SSD |  | ✔ | 
|  `i2.8xlarge`  |  8 x 800 GB \(6,400 GB\)  | SSD |  | ✔ | 
|  `i3.large`  |  1 x 475 GB  | NVMe SSD |  | ✔ | 
|  `i3.xlarge`  |  1 x 950 GB  | NVMe SSD |  | ✔ | 
|  `i3.2xlarge`  |  1 x 1,900 GB  | NVMe SSD |  | ✔ | 
|  `i3.4xlarge`  |  2 x 1,900 GB \(3\.8 TB\)  | NVMe SSD |  | ✔ | 
|  `i3.8xlarge`  |  4 x 1,900 GB \(7\.6 TB\)  | NVMe SSD |  | ✔ | 
|  `i3.16xlarge`  |  8 x 1,900 GB \(15\.2 TB\)  | NVMe SSD |  | ✔ | 
|  `m1.small`  |  1 x 160 GB†  | HDD | ✔ |  | 
|  `m1.medium`  |  1 x 410 GB  | HDD | ✔ |  | 
|  `m1.large`  |  2 x 420 GB \(840 GB\)  | HDD | ✔ |  | 
|  `m1.xlarge`  |  4 x 420 GB \(1,680 GB\)  | HDD | ✔ |  | 
|  `m2.xlarge`  |  1 x 420 GB  | HDD | ✔ |  | 
|  `m2.2xlarge`  |  1 x 850 GB  | HDD | ✔ |  | 
|  `m2.4xlarge`  |  2 x 840 GB \(1,680 GB\)  | HDD | ✔ |  | 
|  `m3.medium`  |  1 x 4 GB  | SSD | ✔ |  | 
|  `m3.large`  |  1 x 32 GB  | SSD | ✔ |  | 
|  `m3.xlarge`  |  2 x 40 GB \(80 GB\)  | SSD | ✔ |  | 
|  `m3.2xlarge`  |  2 x 80 GB \(160 GB\)  | SSD | ✔ |  | 
|  `r3.large`  |  1 x 32 GB  | SSD |  | ✔ | 
|  `r3.xlarge`  |  1 x 80 GB  | SSD |  | ✔ | 
|  `r3.2xlarge`  |  1 x 160 GB  | SSD |  | ✔ | 
|  `r3.4xlarge`  |  1 x 320 GB  | SSD |  | ✔ | 
|  `r3.8xlarge`  |  2 x 320 GB \(640 GB\)  | SSD |  | ✔ | 
|  `x1.16xlarge`  |  1 x 1,920 GB  | SSD |  |  | 
|  `x1.32xlarge`  |  2 x 1,920 GB \(3,840 GB\)  | SSD |  |  | 
|  `x1e.xlarge`  |  1 x 120 GB  | SSD |  |  | 
|  `x1e.2xlarge`  |  1 x 240 GB  | SSD |  |  | 
|  `x1e.4xlarge`  |  1 x 480 GB  | SSD |  |  | 
|  `x1e.8xlarge`  |  1 x 960 GB  | SSD |  |  | 
|  `x1e.16xlarge`  |  1 x 1,920 GB  | SSD |  |  | 
|  `x1e.32xlarge`  |  2 x 1,920 GB \(3,840 GB\)  | SSD |  |  | 

\* Volumes attached to certain instances suffer a first\-write penalty unless initialized\. For more information, see [Optimizing Disk Performance for Instance Store Volumes](disk-performance.md)\.

\*\* For more information, see [Instance Store Volume TRIM Support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

† The `c1.medium` and `m1.small` instance types also include a 900 MB instance store swap volume, which may not be automatically enabled at boot time\. For more information, see [Instance Store Swap Volumes](instance-store-swap-volumes.md)\.