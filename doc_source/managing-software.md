# Managing Software on Your Linux Instance<a name="managing-software"></a>

The base distribution of Amazon Linux contains many software packages and utilities that are required for basic server operations\. However, many more software packages are available in various software repositories, and even more packages are available for you to build from source code\.

**Topics**
+ [Updating Instance Software](install-updates.md)
+ [Adding Repositories](add-repositories.md)
+ [Finding Software Packages](find-software.md)
+ [Installing Software Packages](install-software.md)
+ [Preparing to Compile Software](compile-software.md)

It is important to keep software up\-to\-date\. Many packages in a Linux distribution are updated frequently to fix bugs, add features, and protect against security exploits\. For more information, see [Updating Instance Software](install-updates.md)\.

By default, Amazon Linux instances launch with two repositories enabled: `amzn-main` and `amzn-updates`\. While there are many packages available in these repositories that are updated by Amazon Web Services, there may be a package that you wish to install that is contained in another repository\. For more information, see [Adding Repositories](add-repositories.md)\. For help finding packages in enabled repositories, see [Finding Software Packages](find-software.md)\. For information about installing software on an Amazon Linux instance, see [Installing Software Packages](install-software.md)\.

Not all software is available in software packages stored in repositories; some software must be compiled on an instance from its source code\. For more information, see [Preparing to Compile Software](compile-software.md)\.

Amazon Linux instances manage their software using the yum package manager\. The yum package manager can install, remove, and update software, as well as manage all of the dependencies for each package\. Debian\-based Linux distributions, like Ubuntu, use the apt\-get command and dpkg package manager, so the yum examples in the following sections do not work for those distributions\.