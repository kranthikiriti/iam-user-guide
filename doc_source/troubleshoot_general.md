# Troubleshooting General Issues<a name="troubleshoot_general"></a>

Use the information here to help you diagnose and fix access\-denied or other common issues that you might encounter when working with AWS Identity and Access Management \(IAM\)\.

**Topics**
+ [I lost my access keys](#troubleshoot_general_access-keys)
+ [I need to access an old account](#troubleshoot_general_lost-root-creds)
+ [I can't sign in to my account](#troubleshoot_general_cant-sign-in)
+ [I get "access denied" when I make a request to an AWS service](#troubleshoot_general_access-denied-service)
+ [I get "access denied" when I make a request with temporary security credentials](#troubleshoot_general_access-denied-temp-creds)
+ [Policy variables aren't working](#troubleshoot_general_policy-variables-dont-work)
+ [Changes that I make are not always immediately visible](#troubleshoot_general_eventual-consistency)
+ [I am not authorized to perform: iam:DeleteVirtualMFADevice](#troubleshoot_general_access-denied-delete-mfa)

## I lost my access keys<a name="troubleshoot_general_access-keys"></a>

Access keys consist of two parts:
+ **The access key identifier**\. This is not a secret, and can be seen in the IAM console wherever access keys are listed, such as on the user summary page\.
+ **The secret access key**\. This is provided when you initially create the access key pair\. Just like a password, it ***cannot be retrieved later***\. If you lost your secret access key, then you must create a new access key pair\. If you already have the [maximum number of access keys](reference_iam-limits.md#reference_iam-limits-entities), you must delete an existing pair before you can create another\.

For more information, see [Resetting Your Lost or Forgotten Passwords or Access Keys](id_credentials_access-keys_retrieve.md)\.

## I need to access an old account<a name="troubleshoot_general_lost-root-creds"></a>

When you first created your AWS account, you provided an email address and password\. These are your AWS account root user credentials\. If you have an old AWS account that you can no longer access because you have lost or forgotten the password, you can recover the password\. For more information, see [Resetting Your Lost or Forgotten Passwords or Access Keys](id_credentials_access-keys_retrieve.md)\.

If you no longer have access to the email, you should first try to recover access to the email\. If that doesn't work, you can contact AWS Customer Service\.

You can try to recover access to your email using one of the following options:
+ If you own the domain where the email address is hosted, you can add the email address back to your email server\. Alternatively, you can set up a catch\-all for your email account\. The catch\-all setting on your domain "catches all" messages that are sent to email addresses that do not exist in the mail server\. It redirects those messages to a specific email address\. For example, if your AWS account root user email address is `paulo@sample-domain.com`, but you changed your only domain email address to `paulo.santos@sample-domain.com`, then you could set your new email as the catch\-all\. That way, when someone like AWS sends a message to `paulo@sample-domain.com` or any other `text@sample-domain.com`, you receive that message at `paulo.santos@sample-domain.com`\.
+ If the email address on the account is part of your corporate email system, we recommend that you contact your IT system administrators\. They might be able to help you regain access to the email address\.

If you’re still not able to access your AWS account, you can find alternate support options at [Contact us](https://aws.amazon.com//contact-us/) by expanding the **I'm an AWS customer and I'm looking for billing or account support** menu\. When you contact Customer Service, you must provide the following information:
+ All the details that are listed on the account, including your full name, phone number, address, email address, and last four digits of the credit card\. You might need to create a new AWS account for the purpose of contacting Customer Service, but this is necessary to assist in the investigation of your request\.
+ The reason that you're unable to access the email account to receive password reset instructions\.
+ Also request that the support team delete any accounts that you are not using\. It’s a good idea not to have open accounts in your name that could result in charges to you\.

## I can't sign in to my account<a name="troubleshoot_general_cant-sign-in"></a>

When you first created your AWS account, you provided an email address and password\. These are your AWS account root user credentials\. If you can no longer access your account because you have lost or forgotten your password, you can recover that password\. For more information, see [Resetting Your Lost or Forgotten Passwords or Access Keys](id_credentials_access-keys_retrieve.md)\.

If you provided your account email address and password, AWS sometimes requires you to provide a one\-time verification code\. To retrieve the verification code, check the email that is associated with your AWS account for a message from Amazon Web Services\. The email address ends in @amazon\.com or @aws\.amazon\.com\. Follow the directions in the message\. If you don't see the message in your account, check your spam folder\. If you no longer have access to the email, see [I need to access an old account](#troubleshoot_general_lost-root-creds)\.

## I get "access denied" when I make a request to an AWS service<a name="troubleshoot_general_access-denied-service"></a>
+ Verify that you have the identity\-based policy permission to call the action and resource that you have requested\. If any conditions are set, you must also meet those conditions when you send the request\. For information about viewing or modifying policies for an IAM user, group, or role, see [Managing IAM Policies](access_policies_manage.md)\.
+ If you're trying to access a service that supports [resource\-based policies](access_policies_identity-vs-resource.md), such as Amazon S3, Amazon SNS, or Amazon SQS, verify that the policy specifies you as a principal and grants you access\. If you make a request to a service within your account, either your identity\-based policies or the resource\-based policies can grant you permission\. If you make a request to a service in a different account, then both your identity\-based policies and the resource\-based policies must grant you permission\. To view the services that support resource\-based policies, see [AWS Services That Work with IAM](reference_aws-services-that-work-with-iam.md)\.
+ If your policy includes a condition with a key–value pair, review it carefully\. Examples include the [`aws:RequestTag/tag-key`](reference_policies_condition-keys.md) global condition key, the AWS KMS [https://docs.aws.amazon.com/kms/latest/developerguide/policy-conditions.html#conditions-kms-encryption-context](https://docs.aws.amazon.com/kms/latest/developerguide/policy-conditions.html#conditions-kms-encryption-context), and the `ResourceTag/tag-key` condition key supported by multiple services\. Make sure that the key name does not match multiple results\. Because condition key names are not case sensitive, a condition that checks for a key named `foo` matches `foo`, `Foo`, or `FOO`\. If your request includes multiple key–value pairs with key names that differ only by case, then your access might be unexpectedly denied\. For more information, see [IAM JSON Policy Elements: Condition](reference_policies_elements_condition.md)\.
+ If you have a [permissions boundary](access_policies_boundaries.md), verify that the policy that is used for the permissions boundary allows your request\. If your identity\-based policies allow the request, but your permissions boundary does not, then the request is denied\. A permissions boundary controls the maximum permissions that a principal entity \(user or role\) can have\. Resource\-based policies are not limited by permissions boundaries\. Permissions boundaries are not common\. For more information about how AWS evaluates policies, see [Policy Evaluation Logic](reference_policies_evaluation-logic.md)\.
+ If you are signing requests manually \(without using the [AWS SDKs](http://aws.amazon.com/tools/)\), verify that you have correctly [signed the request](https://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html)\.

## I get "access denied" when I make a request with temporary security credentials<a name="troubleshoot_general_access-denied-temp-creds"></a>
+ First, make sure that you are not denied access for a reason that is unrelated to your temporary credentials\. For more information, see [I get "access denied" when I make a request to an AWS service](#troubleshoot_general_access-denied-service)\.
+ Verify that the service accepts temporary security credentials, see [AWS Services That Work with IAM](reference_aws-services-that-work-with-iam.md)\.
+ Verify that your requests are being signed correctly and that the request is well\-formed\. For details, see your [toolkit](http://aws.amazon.com/tools/) documentation or [Using Temporary Security Credentials to Request Access to AWS Resources](id_credentials_temp_use-resources.md)\.
+ Verify that your temporary security credentials haven't expired\. For more information, see [Temporary Security Credentials](id_credentials_temp.md)\. 
+ Verify that the IAM user or role has the correct permissions\. Permissions for temporary security credentials are derived from an IAM user or role\. As a result, the permissions are limited to those that are granted to the role whose temporary credentials you have assumed\. For more information about how permissions for temporary security credentials are determined, see [Controlling Permissions for Temporary Security Credentials](id_credentials_temp_control-access.md)\.
+ If you are accessing a resource that has a resource\-based policy by using a role, verify that the policy grants permissions to the role\. For example, the following policy allows `MyRole` from account `111122223333` to access `MyBucket`\.

  ```
  {
    "Version": "2012-10-17",
    "Statement": [{
      "Sid": "S3BucketPolicy",
      "Effect": "Allow",
      "Principal": {"AWS": ["arn:aws:iam::111122223333:role/MyRole"]},
      "Action": ["s3:PutObject"],
      "Resource": ["arn:aws:s3:::MyBucket/*"]
    }]
  }
  ```

## Policy variables aren't working<a name="troubleshoot_general_policy-variables-dont-work"></a>
+ Verify that all policies that include variables include the following version number in the policy: `"Version": "2012-10-17"`\. Without the correct version number, the variables are not replaced during evaluation\. Instead, the variables are evaluated literally\. Any policies that don't include variables will still work if you include the latest version number\.

  A `Version` policy element is different from a policy version\. The `Version` policy element is used within a policy and defines the version of the policy language\. A policy version, on the other hand, is created when you make changes to a customer managed policy in IAM\. The changed policy doesn't overwrite the existing policy\. Instead, IAM creates a new version of the managed policy\. To learn more about the `Version` policy element see [IAM JSON Policy Elements: Version](reference_policies_elements_version.md)\. To learn more about policy versions, see [Versioning IAM Policies](access_policies_managed-versioning.md)\.
+ Verify that your policy variables are in the right case\. For details, see [IAM Policy Elements: Variables and Tags](reference_policies_variables.md)\.

## Changes that I make are not always immediately visible<a name="troubleshoot_general_eventual-consistency"></a>

As a service that is accessed through computers in data centers around the world, IAM uses a distributed computing model called [eventual consistency](https://wikipedia.org/wiki/Eventual_consistency)\. Any change that you make in IAM \(or other AWS services\) takes time to become visible from all possible endpoints\. Some of the delay results from the time it takes to send the data from server to server, from replication zone to replication zone, and from region to region around the world\. IAM also uses caching to improve performance, but in some cases this can add time: The change might not be visible until the previously cached data times out\.

You must design your global applications to account for these potential delays\. Ensure that they work as expected, even when a change made in one location is not instantly visible at another\. Such changes include creating or updating users, groups, roles, or policies\. We recommend that you do not include such IAM changes in the critical, high\-availability code paths of your application\. Instead, make IAM changes in a separate initialization or setup routine that you run less frequently\. Also, be sure to verify that the changes have been propagated before production workflows depend on them\. 

For more information about how some other AWS services are affected by this, consult the following resources:
+ **Amazon DynamoDB**: [What is the consistency model of Amazon DynamoDB?](https://aws.amazon.com/dynamodb/faqs) in the *DynamoDB FAQ*, and [Read Consistency](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadConsistency.html) in the Amazon DynamoDB Developer Guide\.
+ **Amazon EC2**: [EC2 Eventual Consistency](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/query-api-troubleshooting.html#eventual-consistency) in the *Amazon EC2 API Reference*\.
+ **Amazon EMR**: [Ensuring Consistency When Using Amazon S3 and Amazon Elastic MapReduce for ETL Workflows](http://aws.amazon.com/blogs/big-data/ensuring-consistency-when-using-amazon-s3-and-amazon-elastic-mapreduce-for-etl-workflows/) in the AWS Big Data Blog
+ **Amazon Redshift**: [Managing Data Consistency](https://docs.aws.amazon.com/redshift/latest/dg/managing-data-consistency.html) in the *Amazon Redshift Database Developer Guide*
+ **Amazon S3**: [Amazon S3 Data Consistency Model](https://docs.aws.amazon.com/AmazonS3/latest/dev/Introduction.html#ConsistencyModel) in the *Amazon Simple Storage Service Developer Guide*

## I am not authorized to perform: iam:DeleteVirtualMFADevice<a name="troubleshoot_general_access-denied-delete-mfa"></a>

You might receive the following error when you attempt to assign an MFA device to yourself or another user:

```
User: arn:aws:iam::123456789012:user/Diego is not authorized to perform: iam:DeleteVirtualMFADevice on resource: arn:aws:iam::123456789012:mfa/Diego with an explicit deny
```

This could happen if someone previously began assigning a virtual MFA device to a user in the IAM console and cancelled the process\. This creates an MFA device for the user in IAM but never fully associates it with the user\. The IAM console requires that the old MFA device be deleted before allowing you to associate a new device with the user\. If you do not have a policy that allows you to delete a virtual MFA device, then you are denied access to associate a new MFA key with the user\.